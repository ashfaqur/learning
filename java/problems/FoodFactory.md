# Food Factory

https://www.hackerrank.com/challenges/java-factory/

## Solution

```java
import java.io.*;
import java.util.*;

public class Solution {

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String input = scanner.nextLine();
        Food food = FoodFactory.getFood(input);
        System.out.println("The factory returned class " + food.getType());
        System.out.println(food);
        
        
    }
}

class FoodFactory {
    
    public static Food getFood(String type){
        switch (type){
            case "cake":
                return new Cake();
            case "pizza":
                return new Pizza();
            default:
                throw new IllegalArgumentException("Unknown type");
        }
    }
    
}

interface Food {
    public String getType();
}

class Cake implements Food{
    
    @Override
    public String getType(){
        return "Cake";
    }
    
    @Override
    public String toString(){
        return "Someone ordered a Dessert!";
    }
}

class Pizza implements Food{
    
    @Override
    public String getType(){
        return "Pizza";
    }
    
    @Override
    public String toString(){
        return "Someone ordered a Fast Food!";
    }
}

```
