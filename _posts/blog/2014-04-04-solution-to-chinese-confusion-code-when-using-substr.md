---
layout: post
title: PHP substr截取中文字符出现乱码问题解惑
description: PHP substr截取中文字符时通常会出现乱码。这是因为中文UTF-8编码，每个汉字占3字节，而GB2312占2字节，英文占1字节，截取位不准确，造成断开的字符会把其后的内容拉过来一起做一个字，所以出现了乱码。我们可以通过使用mbstring扩展库的mb_substr截取；或者自己书写截取函数等方式来解决这一问题。
category: blog
---

## 问题描述

在很多时候为了网站页面排版的整齐，会对从数据库取出的内容进行截断+'...'处理。一般来说有两种处理方式: CSS代码前端处理；PHP后端程序处理。[PHP substr()](http://www.w3school.com.cn/php/func_string_substr.asp)函数返回字符串的一部分，用法`substr(string,start,length)`,按照道理来说能处理我们需求。然而在实际使用中，在最后截断内容会出现@@字符乱码。

## 问题发生原因
PHP 自带几种字符串截取函数，其中最常用到的就是 `substr` 和 `mb_substr`(需要服务器php.ini开启该扩展)。前者在处理中文时，GBK 为 2 个长度单位，UTF 为 3 个长度单位，后者指定编码后，一个中文即为 1 个长度单位。

所以在使用 `substr`截断中文字符串时候会截1/3个中文或半个中文，截取位不准确，造成断开的字符会把其后的..拉过来一起做一个字，所以出现了乱码。


## 问题解决思路

* 1、改用 `mb_substr()` 函数

相对来说 `mb_substr` 更适合我们使用，`string mb_substr ( string $str , int $start [, int $length [, string $encoding ]] )`,类似 `substr()` 函数，只是计数按字符数来计，保证字符安全。不过需要在php.ini开启 `mbstring` 扩展。

然而，有的时候 `mb_substr` 就显得不那么好用了。例如我要显示一个小图片的简要信息，5 个中文正好，超过 5 个就截取前4再加上 ”…”，这样处理中文是没问题了，可是处理英文或数字，这样截取就太短了。

由此可见，使用 `mb_substr()` 函数可保证不会出现乱码，但缺点是长度统计变成了字符数统计，而不是按字节数统计。用于显示时，同样长度的中文结果和英文结果会出现较大的显示长度的差别。

* 2、自己书写截取函数

以下为我自己封装的截取函数，中文字符按2个长度单位来计算，使得中英文混用环境下字符串截取结果最后的显示长度接近；舍弃最后一个不完整字符，保证不会出现显示上的乱码；且兼容了中文字符常用的utf-8编码和GB2312编码，有很好的通用性。缺点是效率不如用 `mbstring` 扩展库来得高。

```
function getStrLen($str,$mutil,$max=null){//中英文名字混合截断到一定长度
	$now = 0;
	$leng = strlen($str);
	$size = 0;
	$new_str = '';
	$result = array();
	while($now<$leng){
		if(ord($str[$now])>128){
			$new_str .= substr($str,$now,3);
			$now += 3;
			$size += $mutil;
		}else{
			$new_str .= substr($str,$now,1);
			$now += 1;
			$size++;
		}
		if($size>=$max && $max){
			$result['size'] = $size;
			$result['str'] = $new_str;
			return $result;
		}
	}
	$result['size'] = $size;
	$result['str'] = $new_str;
	return $result;
}
```

* 3、如果仅是为了输出截取的串，可用如下方式实现：`substr($str, 0, 30).chr(0)`。

## 扩展研究

`substr()` 函数可以分割文字，但要分割的文字如果包括中文字符往往会遇到问题，这时可以用 `mb_substr()/mb_strcut` 这个函数，`mb_substr()/mb_strcut` 的用法与 `substr()` 相似，只是在 `mb_substr()/mb_strcut` 最后要加入多一个参数，以设定字符串的编码，但是一般的服务器都没打开 `php_mbstring.dll` ， `php.ini` 在把 `php_mbstring.dll` 打开。

举个PHP `substr` 截取中文字符的例子：

```
< ?php  
echo mb_substr('这样一来我的字符串就不会有乱码^_^', 0, 7, 'utf-8');  
?> 
输出：这样一来我的字  
< ?php  
echo mb_strcut('这样一来我的字符串就不会有乱码^_^', 0, 7, 'utf-8');  
?> 
```

输出：这样一

从上面的例子可以看出，`mb_substr` 是按字来切分字符，而`mb_strcut` 是按字节来切分字符，但是都不会产生半个字符的现象。

## 总结

以上就是PHP `substr` 截取中文字符时出现乱码的原因及解决办法的介绍，不得不说，编码问题的坑还真多。




