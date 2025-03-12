## PTA难题

6



9



11

```java
public class Main {
    public static void main(String[] args) throws IOException {
        // 创建 BufferedReader 对象，用于高效读取输入
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));

        String str1 = br.readLine();
        String str2 = br.readLine();

        // 用 HashSet 存储 str2 中的字符，便于快速查找是否存在某字符（O(1) 查找）
        Set<Character> set = new HashSet<>();
        for (char c : str2.toCharArray()) set.add(c);  // 将 str2 中的每个字符加入集合

        // 用 StringBuilder 来拼接最终结果，减少频繁的 System.out.print 操作，提升效率
        StringBuilder sb = new StringBuilder();
        for (char c : str1.toCharArray())
            // 如果当前字符不在 set 中，说明不是要过滤的字符，添加到结果中
            if (!set.contains(c)) sb.append(c);

        System.out.print(sb);
    }
}
```

