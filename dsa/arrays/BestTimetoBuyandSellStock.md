# Best Time to Buy and Sell Stock

You are given an array prices where prices[i] is the price of a given stock on the ith day.

You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/

## Solution Optimal

minPrice → tracks the lowest price seen so far.

maxProfit → tracks the maximum profit so far.

Time O(n)
Space O(1)

```java
public int maxProfit(int[] prices) {
    if (prices.length == 0) {
        return 0;
    }
    int maxProfit = 0;
    int minPrice = prices[0];
    for (int i = 1; i < prices.length; i++) {
        minPrice = Math.min(minPrice, prices[i]);
        maxProfit = Math.max(maxProfit, prices[i] - minPrice);
    }
    return maxProfit;
}
```

## Solution Brute

Time O(n2)
Space O(1)

```java
public int maxProfit(int[] prices) {
    int maxProfit = 0;
    for (int i = 0; i < prices.length; i++) {
        int buy = prices[i];
        for (int j = i + 1; j < prices.length; j++) {
            int sell = prices[j];
            int profit = sell - buy;
            if (profit > maxProfit) {
                maxProfit = profit;
            }
        }
    }
    return maxProfit;
}
```

