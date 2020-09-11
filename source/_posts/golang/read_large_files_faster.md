---
title: "利用多线程在Go中更快地读取大文件"
tags:
  - golang
categories:
  - 技术文章
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 利用多线程在Go中更快地读取大文件

如何计算具有4gb RAM的50gb文件中单词的出现次数。窍门是不将整个文件加载到内存中，而是在继续移动文件指针时继续处理每个单词。这样，我们可以用最少的内存资源轻松处理整个文件。

现在的后续问题是我们如何使用多线程来加快此过程？解决方案是我们在文件的不同部分保留多个指针，并且每个线程同时读取文件的块。

最后，结果可以合并。

<!--more-->

## 解决方案

假设文件的大小为1GB。5个线程中的每个线程将处理200MB。连续指针将从上一个指针的最后一个读取字节的字节开始读取。

```go
const mb = 1024 * 1024
const gb = 1024 * mb

func main() {
	// A waitgroup to wait for all go-routines to finish.
	wg := sync.WaitGroup{}

	// This channel is used to send every read word in various go-routines.
	channel := make(chan (string))

	// A dictionary which stores the count of unique words.
	dict := make(map[string]int64)

	// Done is a channel to signal the main thread that all the words have been
	// entered in the dictionary.
	done := make(chan (bool), 1)

	// Read all incoming words from the channel and add them to the dictionary.
	go func() {
		for s := range channel {
			dict[s]++
		}

		// Signal the main thread that all the words have entered the dictionary.
		done <- true
	}()

	// Current signifies the counter for bytes of the file.
	var current int64

	// Limit signifies the chunk size of file to be processed by every thread.
	var limit int64 = 500 * mb

	for i := 0; i < 2; i++ {
		wg.Add(1)

		go func() {
			read(current, limit, "gameofthrones.txt", channel)
			fmt.Printf("%d thread has been completed \n", i)
			wg.Done()
		}()

		// Increment the current by 1+(last byte read by previous thread).
		current += limit + 1
	}

	// Wait for all go routines to complete.
	wg.Wait()
	close(channel)

	// Wait for dictionary to process all the words.
	<-done
	close(done)
}

func read(offset int64, limit int64, fileName string, channel chan (string)) {
	file, err := os.Open(fileName)
	defer file.Close()

	if err != nil {
		panic(err)
	}

	// Move the pointer of the file to the start of designated chunk.
	file.Seek(offset, 0)
	reader := bufio.NewReader(file)

	// This block of code ensures that the start of chunk is a new word. If
	// a character is encountered at the given position it moves a few bytes till
	// the end of the word.
	if offset != 0 {
		_, err = reader.ReadBytes(' ')
		if err == io.EOF {
			fmt.Println("EOF")
			return
		}

		if err != nil {
			panic(err)
		}
	}

	var cummulativeSize int64
	for {
		// Break if read size has exceed the chunk size.
		if cummulativeSize > limit {
			break
		}

		b, err := reader.ReadBytes(' ')

		// Break if end of file is encountered.
		if err == io.EOF {
			break
		}

		if err != nil {
			panic(err)
		}

		cummulativeSize += int64(len(b))
		s := strings.TrimSpace(string(b))
		if s != "" {
			// Send the read word in the channel to enter into dictionary.
			channel <- s
		}
	}
}
```



在这里，我们还必须处理边缘情况。如果块的开头不是新单词的开头怎么办。类似地，如果块的结尾不是块的结尾怎么办。

我们通过将块的末尾扩展到单词的末尾并将连续块的开头移到下一个单词的开头来处理此问题。

我们正在使用渠道将各个线程读取的所有单词统一到一个字典中。同步。Waitgroup可用于线程同步，并确保所有线程都已完成文件读取

## 结果

与串行方式相比，可以观察到性能提高了一倍，同时处理1GB文件所需的时间减少了一半。

我们之所以无法获得5倍的性能（即线程数），是因为尽管goroutine是轻量级的线程，但读取文件的过程需要一个完整os级CPU内核的资源。它没有睡眠时间。因此，在双核系统上，它只能有效地使文件处理性能提高一倍。