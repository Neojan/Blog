## 初始化
```cpp
string str;  //定义了一个空字符串str
    str = "Hello world";   // 给str赋值为"Hello world"
    char cstr[] = "abcde";  //定义了一个C字符串
    string s0 = cstr;
    string s1(str);       //调用复制构造函数生成s1，s1为str的复制品
    string s2(str,6);     //将str内，开始于位置6的部分当作s2的初值
    string s3(str,6,3);  //将str内，开始于6且长度顶多为3的部分作为s3的初值
    string s4(cstr);   //将C字符串作为s4的初值
    string s5(cstr,3);  //将C字符串前3个字符作为字符串s5的初值。
    string s6(5,'A');  //生成一个字符串，包含5个'A'字符
    string s7(str.begin(),str.begin()+5); //区间str.begin()和str.begin()+5内的字符作为初值
    
```

## 比较
可以用\=\= 、>、<、>=、<=、和!=比较字符串，可以用+或者+=操作符连接两个字符串，并且可以用[]获取特定的字符。

```cpp
    string str;
    cout << "Please input your name:"<<endl;
    cin >> str;
    if( str == "Li" )   // 字符串相等比较
        cout << "you are Li!"<<endl;
    else if( str != "Wang" )  // 字符串不等比较
        cout << "you are not Wang!"<<endl;
    else if( str < "Li")     // 字符串小于比较，>、>=、<=类似
        cout << "your name should be ahead of Li"<<endl;
    else
        cout << "your name should be after of Li"<<endl;
    str += ", Welcome!";  // 字符串+=
    cout << str<<endl;
    for(int i = 0 ; i < str.size(); i ++)
        cout<<str[i];  // 类似数组，通过[]获取特定的字符
```

## 其他
```cpp
str.size();
str.empty();
str.length();
const char* pszOutput = strOutput.c_str();
//int转string
int nNum2 = 456;
string strTest4; 
strTest4 = to_string(nNum2);
reverse(s.begin(), s.end());//反转原先字符串
```

## find
```cpp
    int loc;
    string s="study hard and make progress everyday! every day!!";
    loc=s.rfind("make",10);//-1表示没找到
    loc=s.rfind("make");//缺省状态下，从最后一个往前找
    loc=s.find_first_of("day");
    loc=s.find_first_not_of("study");
    loc=s.find_last_of("day");
    loc=s.find("day");//缺陷状态下从第一个往后找
    // 如果检索到子串在字符串中，则打印子串的位置
    if (loc != string::npos)
    {
    }
    size_t pos = str.find("live"); // position of "live" in str
	string str3 = str.substr(pos); // get from "live" to the end
```

## 大小写转化
tolower
toupper
stoi ：str转整型
to_string：数字转字符串
isalpha(int);
isnumber(int);

bitset - 二进制
bitset<32> b(str or int);