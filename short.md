
# Markdown Visualization

![Drag Racing](//placekitten.com/1500/500 "title1")
![Drag Racing](//placekitten.com/800/1200 "cat")

![](https://www.youtube.com/watch?v=6GvPSR5u-j8 "cat")

This feature allows user creating flexible interactive visualization with ease.

~~~[slideshow](height=300)
~~~


~~ {.tabset}

## Go

```go
package sort

// SelectionSort sorts array of items
func SelectionSort(arr []Item) {
	n := len(arr)
	for i := 0; i < n-1; i++ {
		for j := i + 1; j < n; j++ {
			if arr[j].Less(arr[i]) {
				swap(arr, i, j)
			}
		}
	}
}
```

## Ts

```
import { swap } from './common'

// SelectionSort sorts array of items
export function selectionSort(a: number[]) {
  const n = a.length
  for (let i = 0; i < n-1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (a[j] < a[i]) {
        swap(a, i, j)
      }
    }
  }
}
```

~~ {-}

<strong>abcd</strong>

(Q-1) **This is question 1**

- 
  ```js
  var a = 1
  let x = 2
  for (i = 1; i < 10; i++) {
    printf(i)
  }
  ```
- answer2
- answer3

(Q-2) **This is question 2**

- 
  ~~~[tree](itemShape=circle,itemSize=30,height=250)
  1:(4)
  2:(2)|3:(5)
  4:(1)|5:(3)
  ~~~

- answer5
- answer6
