# 绿荫工作室2022年招新最终审核
### 请在本目录下或者本文件下提交项目代码/截图:

计1907李牧霖: 我在等着我的小学弟/学妹们提交文件~
#include <iostream>　
#include<string>
#include<stack>
using namespace std;
/*
介绍：一段8位十六进制整数表达式求值程序。以一个字符串作为参数，字符串是由八位十六进制整数构成的表达式，可支持+ - * / % &  |  ^ ~运算。不允许括号和空格
	11111111+99999999 
输出
	十进制：
	2863311530
	十六进制：
	aaaaaaaa

输入：
	1234abcd+23456789*3456789a+456789ab%56789abc
输出
	十进制：
	519606065611584994
	十六进制：
	73602f6b3eab5e2




思路：
	1.处理8位十六进制数即为32位无符号整型,防止溢出使用64位无符号整型
	2.建立操作数栈和操作符栈，操作符入栈时检查栈顶运算符优先级。
	如果栈顶操作符优先级高于当前运算符，栈顶运算符出栈并执行运算。
	直到栈顶运算符优先级小于当前运算符优先级。
	3.本题中未考虑括号问题，如果要处理括号，应当在扫描到左括号时直接入栈。扫描到右括号时，将栈中运算符依次取出计算，直到遇到左括号

程序通过建立操作符栈和操作数栈，实现只扫描一次输入串。整体思路参考编译原理中处理算术表达式的词法扫描器
*/

unsigned long long toNumber(string hexStr) {//将8位十六进制数转换为32位无符号整型
	unsigned long long operand=0;
	for (int i = 0; i < 8; i++) {
		unsigned long long codeAt = hexStr[i] - '0';
		if (codeAt >= 49) {//小写转大写
			codeAt -= 32;
		}
		if (codeAt >= 17) {//处理a-f
			codeAt -= 7;
		}
		operand = (operand << 4) + codeAt;
	}
	return operand;
}
unsigned long long calc(char Operator, unsigned long long leftOperand, unsigned long long rightOperand) {//计算，并返回结果
	unsigned long long result;
	if (Operator == '+') {
		result = leftOperand+rightOperand;
		return result;
	}
	else if (Operator == '-') {
		result = leftOperand - rightOperand;
		return result;
	}
	else if (Operator == '*') {
		result = leftOperand * rightOperand;
		return result;
	}
	else if (Operator == '/') {
		result = leftOperand / rightOperand;
		return result;
	}
	else if (Operator == '%') {
		result = leftOperand % rightOperand;
		return result;
	}
	else if (Operator == '&') {
		result = leftOperand & rightOperand;
		return result;
	}
	else if (Operator == '|') {
		result = leftOperand | rightOperand;
		return result;
	}
	else if (Operator == '^') {
		result = leftOperand ^ rightOperand;
		return result;
	}
	else if (Operator == '~') {
		result = ~rightOperand;
		return result;
	}
}
int main() {
	stack<unsigned long long> operands;//操作数栈
	stack<char> operators;//操作符栈
	printf("Input:");
	string str;
	cin >> str;
	int len = str.length();
	int index = 0;
	
	while (index < len) {
		if(str[index]=='~'){//~优先级最高直接入栈
			operators.push(str[index]);
			index++;
			continue;
		}
		else if (str[index] == '*'|| str[index] == '/'|| str[index] == '%') {//%/*优先级相同
			while (!operators.empty()&&(operators.top()=='~'||operators.top() == '*'||operators.top() == '/' || operators.top() == '%')) {
				//优先级更高的直接出栈计算，结果入栈
				unsigned long long left,right;
				char Operator = operators.top();
				operators.pop();
				if (Operator == '~') {//~出栈计算时~是单目运算符
					right=operands.top();
					operands.pop();
					left = 1;
				}
				else {
					right = operands.top();
					operands.pop();
					left = operands.top();
					operands.pop();
				}
				operands.push(calc(Operator,left,right));
			}
			operators.push(str[index]);
			index++;
			continue;
		}
		else if (str[index] == '+' || str[index] == '-' ) {
			
			while (!operators.empty()&&(operators.top() == '~' || operators.top() == '*' || operators.top() == '/' || operators.top() == '%' || operators.top() == '+' || operators.top() == '-')) {
				unsigned long long left, right;
				char Operator = operators.top();
				operators.pop();
				if (Operator == '~') {
					right = operands.top();
					operands.pop();
					left = 1;
				}
				else {
					right = operands.top();
					operands.pop();
					left = operands.top();
					operands.pop();
				}
				operands.push(calc(Operator, left, right));
			}
			operators.push(str[index]);
			index++;
			continue;
		}
		else if (str[index] == '&' ) {
			while (!operators.empty()&&(operators.top() == '~' || operators.top() == '*' || operators.top() == '/' || operators.top() == '%' || operators.top() == '+' || operators.top() == '-' || operators.top() == '&') ) {
				unsigned long long left, right;
				char Operator = operators.top();
				operators.pop();
				if (Operator == '~') {
					right = operands.top();
					operands.pop();
					left = 1;
				}
				else {
					right = operands.top();
					operands.pop();
					left = operands.top();
					operands.pop();
				}
				operands.push(calc(Operator, left, right));
			}
			operators.push(str[index]);
			index++;
			continue;
		}
		else if (str[index] == '^') {
			while (!operators.empty() && (operators.top() == '~' || operators.top() == '*' || operators.top() == '/' || operators.top() == '%' || operators.top() == '+' || operators.top() == '-' || operators.top() == '&' || operators.top() == '^')) {
				unsigned long long left, right;
				char Operator = operators.top();
				operators.pop();
				if (Operator == '~') {
					right = operands.top();
					operands.pop();
					left = 1;
				}
				else {
					right = operands.top();
					operands.pop();
					left = operands.top();
					operands.pop();
				}
				operands.push(calc(Operator, left, right));
			}
			operators.push(str[index]);
			index++;
			continue;
		}
		else if (str[index] == '|') {
			while (!operators.empty() && (operators.top() == '~' || operators.top() == '*' || operators.top() == '/' || operators.top() == '%' || operators.top() == '+' || operators.top() == '-' || operators.top() == '&' || operators.top() == '^'||operators.top() == '|')) {
				unsigned long long left, right;
				char Operator = operators.top();
				operators.pop();
				if (Operator == '~') {
					right = operands.top();
					operands.pop();
					left = 1;
				}
				else {
					right = operands.top();
					operands.pop();
					left = operands.top();
					operands.pop();
				}
				operands.push(calc(Operator, left, right));
			}
			operators.push(str[index]);
			index++;
			continue;
		}
		else{//扫描到八位十六进制数字符串
			string hexStr=str.substr(index, 8);
			unsigned long long hexNum = toNumber(hexStr);
			operands.push(hexNum);
			index += 8;
		}
	}
	while (!operators.empty()) {//扫描结束后，若操作符栈中仍有残留，依次出栈计算
		unsigned long long left, right;
		char Operator = operators.top();
		operators.pop();
		if (Operator == '~') {
			right = operands.top();
			operands.pop();
			left = 1;
		}
		else {
			right = operands.top();
			operands.pop();
			left = operands.top();
			operands.pop();
		}
		operands.push(calc(Operator, left, right));
	}
	printf("十进制：\n %llu \n", operands.top());
	printf("十六进制：\n %llx \n", operands.top());
}
