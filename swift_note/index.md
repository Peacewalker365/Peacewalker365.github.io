# Swift

# Swift

## Optional

## Pointer

## ~>

## is as

## closure
```Swift
let coffee: [String] = ["Cappuccino", "Espresso", "Latte", "Ristretto"]

// normal
func backward(_ n1: String, _ n2: String) -> Bool {
    return n1 > n2
}
var reverseOrder = coffee.sorted(by: backward)


/* RESULT: ["Ristretto", "Latte", "Espresso", "Cappuccino"] */

// inline
reverseOrder = coffee.sorted(by: { (n1: String, 
                                    n2: String) -> Bool in return n1 > n2 } )

// inferring type from context
reverseOrder = coffee.sorted(by: { n1, n2 in return n1 > n2 } )

// implicit returns from single-expression closure
reverseOrder = coffee.sorted(by: { n1, n2 in n1 > n2 } )

// shorthand arg names
reverseOrder = coffee.sorted(by: { $0 > $1 } )

/* $0 and $1 are closure’s first and second String arguments. */

// operator method
reverseOrder = coffee.sorted(by: >)

/* RESULT: ["Ristretto", "Latte", "Espresso", "Cappuccino"] */

```

## Worth to know
1. for loop is obsoleted in Swift 3.

2. repeat...while in Swift works as do...while of other langs

3. `fallthrough` keyword

4. _ in func paras
  ```Swift
  func myFunc(param1:String, _param2:String, _param3:String) {}
  // when we call the function, we don't need to specify the names.
  // note that the first param is always not externalized by default.
  ```

5. $0 means the first param passed into the closure.
  ```Swift
  let sorted_nums = numbers.sort {$0 > $1}
  print(sorted_nums)

  let sorted_nums = numbers.sort { (first_obj, second_obj) in return first_obj > second_obj}
  ```

6. `typealias` works like `typedef` in C.







