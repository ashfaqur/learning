```java
import java.io.*;
import java.util.*;

public class Solution {

    public static void main(String[] args) {
        /* Enter your code here. Read input from STDIN. Print output to STDOUT.
        Your class should be named Solution. */
        Scanner scanner = new Scanner(System.in);
        int lineNumber = 1;
        while(scanner.hasNextLine()){
            String str = scanner.nextLine();
            System.out.println(lineNumber + " " + str);
            lineNumber++;
        }
    }
}
```
