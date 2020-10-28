---
title: "Golang 多个字段顺序排序"
summary: "Golang 多个字段顺序排序"
date: 2020-10-28T14:05:26+08:00
categories: ["tech"]
---

### 直接上Demo

```go
package main

import (
	"fmt"
	"os"
	"sort"
	"text/tabwriter"
)

type Student struct {
	name string
	age  int
	score int
}

var students []*Student

func printStudents(students []*Student) {
	const format = "%v\t%v\t%v\t\n"
	tw := new(tabwriter.Writer).Init(os.Stdout, 0, 8, 2, ' ', 0)
	fmt.Fprintf(tw, format, "name", "age", "score")
	fmt.Fprintf(tw, format, "----", "---", "---")
	for _, s := range students {
		fmt.Fprintf(tw, format, s.name, s.age, s.score)
	}
	tw.Flush()
}

type studentSort struct {
	s    []*Student
	less func(x, y *Student) bool
}

func (x studentSort) Len() int           { return len(x.s) }
func (x studentSort) Less(i, j int) bool { return x.less(x.s[i], x.s[j]) }
func (x studentSort) Swap(i, j int)      { x.s[i], x.s[j] = x.s[j], x.s[i] }

func init() {
	students = []*Student{
		&Student{"Adam", 20, 60},
		&Student{"Bob", 18, 60},
		&Student{"Clark", 19, 70},
		&Student{"Daisy", 18, 70},
		&Student{"Eva", 20, 80},
		&Student{"Frank", 20, 60},
		&Student{"Gideon", 19, 30},
	}
}

func main() {
	// 第一次可以使用非稳定排序
	sort.Sort(studentSort{students, func(x, y *Student) bool {
		if x.name != y.name {
			return x.name < y.name
		}
		return false
	}})
	// 第二次之后就需要使用稳定排序，不然之前的排序又会被打乱
	sort.Stable(studentSort{students, func(x, y *Student) bool {
		if x.score != y.score {
			return x.score > y.score
		}
		return false
	}})

	sort.Stable(studentSort{students, func(x, y *Student) bool {
		if x.age != y.age {
			return x.age < y.age
		}
		return false
	}})

	printStudents(students)
}
```

### 结果
首先根据年龄升序排序，然后根据成绩降序排序，最后根据名称升序排序

### 参考
https://blog.51cto.com/steed/2390923