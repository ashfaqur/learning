# Big Decimal

https://www.hackerrank.com/challenges/java-bigdecimal/problem?isFullScreen=true


## Solution

```java
import java.io.*;
import java.util.*;
import java.math.*;

public class Solution {
    
    static class NumberStore {
        BigDecimal number;
        String original;
        
        public NumberStore(BigDecimal num, String org){
            this.number  = num;
            this.original = org;
            
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        // List<BigDecimal> numbers = new ArrayList<>();
        List<NumberStore> numbersStore = new ArrayList<>();
        int size = 0;
        int counter = 0;
        
        while (scanner.hasNextLine()){
            String input = scanner.nextLine();
            if (counter > 0){
                // numbers.add(new BigDecimal(input));   
                BigDecimal dec =  new BigDecimal(input);
                numbersStore.add(new NumberStore(dec, input));
            } else {
                size = Integer.valueOf(input);
            }
            counter++;
        }
        
        if (size != numbersStore.size()){
            throw new IllegalArgumentException("Numbers list size " + numbersStore.size() + " does not match expected size " + size);
        }
        
        numbersStore.sort((a, b) -> b.number.compareTo(a.number));
        
        for (NumberStore num : numbersStore){
            System.out.println(num.original);
        }
        
    }
}
```
