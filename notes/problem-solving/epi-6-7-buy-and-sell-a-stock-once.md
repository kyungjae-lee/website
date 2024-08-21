[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Problem Solving</a> > EPI - 6.7. Buy and Sell a Stock Once

# EPI - 6.7. Buy and Sell a Stock Once



## Solutions in C++

### Solution 1

The key is to compute the maximum profit by computing the difference of the current entry with the minimum value seen so far as we iterate through the array.

**Complexity Analysis:**

* $T = O(n)$, where $n$ is the length of the array
* $S = O(1)$ 

**Solution:**

```cpp
#include <vector>

double BuyAndSellStockOnce(const vector<double>& prices)
{
	double min_price_so_far = prices.at(0), max_profit = 0;
    
	for (auto price : prices)
	{
		double max_profit_sell_today = max(price - min_price_so_far, max_profit);
		max_profit = max(max_profit, max_profit_sell_today);
		min_price_so_far = min(min_price_so_far, price);
	}

	return max_profit;
}
```



## Solutions in C

### Solution 1

The key is to compute the maximum profit by computing the difference of the current entry with the minimum value seen so far as we iterate through the array.

**Complexity Analysis:**

* $T = O(n)$, where $n$ is the length of the array
* $S = O(1)$ 

**Solution:**

```cpp
double BuyAndSellStockOnce(const double *prices, int size)
{
	double min_price_so_far = prices[0], max_profit = 0;
	double max_profit_sell_today = 0;

	for (int i = 0; i < size; i++)
	{
		max_profit_sell_today = (prices[i] - min_price_so_far) > max_profit ? 
			(prices[i] - min_price_so_far) : max_profit;

		max_profit = max_profit > max_profit_sell_today ? 
			max_profit : max_profit_sell_today;

		min_price_so_far = min_price_so_far > prices[i] ? 
			prices[i] : min_price_so_far;
	}

	return max_profit;
}
```

