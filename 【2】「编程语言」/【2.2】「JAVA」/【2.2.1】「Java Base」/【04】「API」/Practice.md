```java
package myPr.apix;

import java.util.Arrays;

public class statistic {
    public static void main(String[] args) {
        stringLoad object1 = new stringLoad();
        String str = "asddfa&TTasNN14987652";
        object1.sumNum(str);
        System.out.println("####################");
        object1.upperLowerTimes(str);
        System.out.println("####################");
        object1.sortNum(str);
        System.out.println("####################");
        object1.stringTimes(str);

    }
}




class stringLoad{

    /*
    给定一个字符串，统计字符串大小写英文字母、数字、其他字符的出现次数
     */
    public void upperLowerTimes(String str){
        int lowerLetterTimes = 0;
        int upperLetterTimes = 0;
        int numberTimes = 0;
        int otherStringTimes = 0;
        for(int i=0;i<str.length();i++){
            char c = str.charAt(i);
            if(Character.isDigit(c)){
                numberTimes += 1;
            }else if(c >= 'a' && c <= 'z'){
                lowerLetterTimes += 1;
            }else if(c>='A' && c<='Z'){
                upperLetterTimes += 1;
            }else{
                otherStringTimes += 1;
            }
        }

        System.out.println("小写字母出现次数："+lowerLetterTimes);
        System.out.println("大写字母出现次数："+upperLetterTimes);
        System.out.println("数字出现次数："+numberTimes);
        System.out.println("其他字符出现次数："+otherStringTimes);


    }


    /*
    给定一个字符串，对字符串的数字进行求和
     */
    public void sumNum(String str){
        int sumFinal = 0;
        for(int i=0;i < str.length();i++){

            char c = str.charAt(i);
            if(Character.isDigit(c)){
                sumFinal += c - '0';
            }
        }
        System.out.println("字符串的数字求和:"+sumFinal);
    }


    /*
    给定一个字符串，对字符串的数字进行排序
     */

    public void sortNum(String str){
        int[] arr= new int[str.length()];
        int index = 0;
        for(int i=0;i<str.length();i++){
            char c= str.charAt(i);
            if(Character.isDigit(c)){
                arr[index++] = c - '0';
            }
        }

        //缩容----保证里面只有数字
        arr = Arrays.copyOf(arr,index);
        //排序
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));
    }



    /*
    统计每个字符出现的次数
     */

    public void stringTimes(String str){
        
        boolean[] checkExistArr = new boolean[str.length()];
        for(int i=0;i<str.length();i++){
            checkExistArr[i] = true;
        }

        for(int i=0;i<str.length();i++){
            if(checkExistArr[i]){
                int num=1;
                char c = str.charAt(i);

                // 判断后边是否存在相同字符
                for(int j=i+1;j<str.length();j++){
                    if(c==str.charAt(j)){
                        checkExistArr[j] = false;
                        num += 1;
                    }
                }

                System.out.println(c+": "+ num);

            }
        }

    }

}
```