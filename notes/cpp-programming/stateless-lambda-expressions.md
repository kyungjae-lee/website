[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">C++ Programming</a> > Stateless Lambda Expressions

# Stateless Lambda Expressions



## C++ Stateless Lambda Expressions

Stateless lambda expressions are lambdas that do not capture any external variables from their surrounding scope. An empty capture list means that the expression captures no information from its environment and only has access to the data passed through its function parameter list.

### Simple Stateless Lambda Expressions (No capture lists)

```cpp
[] () { std::cout << "Hi"; } ();		// Displays Hi
int x{10};
[] (int x) { std::cout << x; }(100);
```

> L3: This lambda is invoked with the value `100` passed to its `x` parameter. Since it is stateless, it captures no information from its surrounding environment. This means it has no access to the `x` variable defined earlier and only operates on the value explicitly passed to its parameter when it is called.

```cpp
const int n{3};
int num[n]{10, 20, 30};

auto sum = [] (int nums[], int n)
{
    int sum{0};
    for (int i = 0; i < n; i++)
        sum += nums[i];
    return sum;
};

std:: cout << sum(nums, 3);		// Displays 60
```

> L4: From the empty capture list, we know that this lambda is stateless, meaning it has no access to the array or its length defined earlier. The only way it can compute the sum of the integers in the array is if both the array and its length are passed as parameters. This is exactly what happens when the lambda is called in L12.

### Using Values and References as Lambda Parameters

```cpp
[] (int x) { std::cout << x; };
[] (int &x) { std::cout << x; };
```

> L1: `int x` - Value parameter
>
> L2: `int &x)` - Reference parameter (i.e., alias to the actual parameter; no copy is made)

```cpp
int test_score1{88};
int test_score2{75};

auto bonus = [] (int &score1, int &score2, int bonus_points)
{
    score1 += bonus_points;
    score2 += bonus_points;
};

bonus(test_score1, test_score2, 5);

std::cout << "test_score1: " << test_score1 << std::endl;	// Displays 93
std::cout << "text_score2: " << test_score2 << std::endl;	// Displays 80
```

### Using Pointers as Lambda Parameters

```cpp
int x;
auto l = [] (int *x) { std::cout << *x; }; // '*': dereferencing operator
l(&x);	// '&': Referencing (address-of) operator
```

```cpp
int test_score1{88};
int test_score2{75};

auto bonus = [] (int *score1, int *score2, int bonus_points)
{
    *score1 += bonus_points;
    *score2 += bonus_points;
};

bonus(&test_score1, &test_score2, 5);

std::cout << "test_score1: " << test_score1 << std::endl;	// Displays 93
std::cout << "text_score2: " << test_score2 << std::endl;	// Displays 80
```

### Using Arrays and Vectors as Lambda Reference Parameters

```cpp
std::vector<int> test_scores{93, 88, 75, 68, 65};

auto bonus = [] (std::vector<int> &scores, int bonus_points)
{
    for (int &score : scores)
        score += bonus_points;
};

bonus(test_scores, 5);

std::cout << "test_scores: " << std::endl;
std::cout << text_scores[0] << std::endl;	// Displays 98
std::cout << text_scores[1] << std::endl;	// Displays 93
std::cout << text_scores[2] << std::endl;	// Displays 80
std::cout << text_scores[3] << std::endl;	// Displays 73
std::cout << text_scores[4] << std::endl;	// Displays 70
```

### Using `auto` as Lambda Parameter Type Specifiers

This allows the lambda expression to accommodate different types of arguments, making it more flexible and enabling it to work like a generic function. (The `auto` keyword is the key!)

Note: `auto` is not an actual type. It's an instruction telling the compiler to deduce the actual type.

```cpp
int num1{10};
float num2 {20.5};

auto l = [] (auto x) { std::cout << x; };

l(num1);
l(num2);
```

```cpp
std::vector<int> test_scores1 {93, 88, 75, 68, 65 };
std::vector<float> test_scores2 {88.5, 85.5, 75.5, 68.5, 65.5};

auto bonus = [] (auto &scores, int bonus_points)
{
    for (auto &score : scores)
        score += bonus_points;
};

bonus(test_scores1, 5);		// Valid
bonus(test_scores2, 5);		// Valid
```

### Using Lambda Expressions as Function Parameters

```cpp
#include <functional>		// For std::function

void foo(std::function<void(int)> l) { l(10); }		// C++14
void foo(void (*)(int))	{ l(10); }					// C++14

void foo(auto l) { l(10); }							// C++20
```

> L3: Passing a lambda expression to a function as a **function object** using the standard library's `<functional>` header. The function `foo` takes the function object `l` as a function parameter. `void` type specifier represents the return type of the function object, and the `int` type specifier represents the function object's parameter type. (C++14)
>
> L4: Passing a lambda expression to a function as a **function pointer**. The function `foo` takes as its parameter a pointer to the function `l`. (C++14)
>
> L6: In C++20, we can eliminate the need to explicitly declare return and parameter types by using the `auto` keyword, allowing the compiler to deduce both the parameter types and the return type of the lambda expression.

### Returning Lambda Expressions from Functions

Similar to how the lambda expressions are passed to functions, they can be returned as either function objects, function pointers or by using the `auto` keyword to instruct the compiler to deduce the return type.

```cpp
#include <functional>		// For std::function

std::function<void(int)> foo() { return [] (int x) { std::cout << x; }; }
void (*foo())(int) { return [] (int x) { std::cout << x; }; }

auto foo() { return [] (int x) { std::cout << x; }; }
```

> L3: Returning a lambda expression as a **function object**.
>
> L4: Returning a lambda expression as a **function pointer**. This is an old-style C syntax that has persisted for backward compatibility. In modern C++, it's uncommon to use this approach to return a lambda expression from a function. Instead, it's more typical—and more flexible—to return lambdas either as function objects or by using the `auto` keyword.

All 3 versions are used the same way:

```cpp
auto l = foo();
l(10);		// Displays 10
```

Examples of why you might want to return a lambda from a function are best illustrated using **stateful** lambda expressions, which will be discussed in the next section.

### Using Lambda Expressions as Function Parameters

```cpp
foo([] (int x) { std::cout << x; });

auto l = [] (int x) { std::cout << x; };
foo(l);
```

> L1: A common way of passing lambda expressions to functions since in most cases they're only ever passed once. 
>
> L3: If the lambda will be used more than once, it may be beneficial to assign it to a variable so that it can be passed to multiple functions and called independently without having to define the lambda each time.

### Using Lambda Expressions as Predicates

A predicate in C++ is a function that takes one or more arguments and returns a boolean value. Naturally, a predicate lambda is a lambda expression that implements this behavior. This is where the true power of lambdas shines—enabling concise, inline logic for filtering, searching, and decision-making in algorithms.

```cpp
void print_if(std::vector<int> nums, bool (*predicate)(int))
{
    for (int i : nums)
    {
        if (predicate(i))
            std::cout << i;
    }
}

int main()
{
    std::vector<int> nums{1, 2, 3};
    
    print_if(nums, [] (auto x) { return x % 2 == 0; });	// Displays evens
    print_if(nums, [] (auto x) { return x % 2 != 0; });	// Displays odds
    
    return 0;
}
```

> L1: Takes as its parameters an integer vector and a predicate lambda that's passed as a function pointer. In this case, the predicate lambda is used to determine which elements of the integer vector to display.

Predicate lambdas are especially important when working with Standard Template Library (STL) functions and algorithms such as `std::sort()` or `std::for_each()`, which often take a predicate as a parameter to customize their behavior.
