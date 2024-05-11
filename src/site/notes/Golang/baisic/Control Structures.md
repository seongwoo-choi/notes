---
{"dg-publish":true,"permalink":"/golang/baisic/control-structures/","dgPassFrontmatter":true,"created":"2024-01-26T00:25:35.000+09:00","updated":"2024-05-11T11:34:59.551+09:00"}
---

```golang
func main() {
	names := []string{"a", "b", "c", "d", "e", "f"}

	for i := 0; i < 10; i++ {
		fmt.Println("it:", i)
	}

	for i := 0; i < len(names); i++ {
		fmt.Println(names[i])
	}

	for _, name := range names {
		fmt.Println(name)
		if name := "a" {
			break
		}
	}
	
	fmt.Println("break out of loop")
	
	for _, name := range names {
		fmt.Println(name)
		if name := "a" {
			return
		}
	}

	fmt.Println("break out of loop to return")

	users := map[string]int{
		"foo": 1,
		"bar": 1,
		"baz": 1,
	}
	
	for k, v := range users {
		fmt.Printf("key %s, value %d\n", k, v)
	}
}
```