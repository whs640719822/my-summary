# 两数之和

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
//[1,2,5,6,8]  9
var twoSum = function(nums, target) {
    let map = {};//key数字 value下标
    let loop = 0;//循环次数
    let dis;//目标与当前值的差
    while(loop < nums.length){
        dis = target - nums[loop];
        if(map[dis] != undefined){
            return [map[dis], loop];
        }
        map[nums[loop]] = loop;
        loop++;
    }
    return;
};
```

# 整数反转

```js
var reverse = function(x) {
    //解法1，转成字符串反转
    // let now = Math.abs(x).toString().split("").reverse().join("");
    //解法2，321 = 123%10 12%10 1%10
    let ord = Math.abs(x);//去符号
    let now = 0;
    while(ord > 0){
        now = now * 10 + ord % 10;
        ord = Math.floor(ord / 10);
    }

    //公用部分
    if(x < 0){
        return now <= Math.pow(2,31) ? -now : 0;
    }else{
        return now < Math.pow(2,31) ? now : 0;
    }
};

```

# 回文数

```js
var isPalindrome = function(x) {
    <!-- 如果是负数，或者以0结尾，但是本身是非0整数，一定不是回文数 -->
    if (x < 0 || (x % 10 === 0 && x !== 0)) {
        return false;
    }

    let rev = 0;
    <!-- 从最低位起，依次将末尾数取出来 -->
    while (rev < x) {
        rev = rev * 10 + x % 10;
        x = ~~(x / 10);
    }		
    <!-- 第一种情况是针对偶数位数的情况，第二种情况是针对奇数位数的情况 -->
    return (rev === x) || (~~(rev / 10) === x)
};
```

# 最长公共前缀

```js
/**
 * @param {string[]} strs
 * @return {string}
 */
var longestCommonPrefix = function(strs) {
    if(!strs.length) return '';
    let [a, ...b] = strs;
    let result = '';
    for(let i = 0; i < a.length; i++){
        let flag = b.every(item => item[i] === a[i]);
        if(flag) result += a[i];
        else break;
    }
    return result;
};

```

# 有效的括号

```js
var isValid = function (s) {
    var map = {
        "(": ")",
        "[": "]",
        "{": "}"
    }
    var leftArr = []
    for (var ch of s){
        if (ch in map) leftArr.push(ch); //为左括号时，顺序保存
        else { //为右括号时，与数组末位匹配
            if(ch != map[leftArr.pop()]) return false;
        }
    }
    return !leftArr.length //防止全部为左括号
};
```

