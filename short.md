
# Markdown Visualization

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

(Q) **This is question 1**

- answer1
- answer2
- answer3
