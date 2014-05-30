---
layout: post
title: 关于MYSQL截断内容问题解决
description: 当MYSQL进行数据存储处理utf8等数据时对某些字符导致数据截断;在做移动应用时，会遇到IOS用户会在文本的区域输入emoji表情，如果不做一定处理，就会导致插入数据库异常。
category: blog
---

##问题描述
<ul>
    <li>当用户发表有某些特殊字符的内容时，存入MYSQL数据库的内容会被截断;</li>
    <li>数据库、程序文件等都是UTF-8编码</li>
    <li>确认SQL语句正常</li>
</ul>

##问题发生原因
<ul>
    <li>MYSQL中UTF-8编码最多只能是3个字节（5.5.3版本后，有 utf8mb4类型可支持4个字节的utf8）；</li>
    <li>utf8是一种1-4个字节的可变字符编码（英文1个字符，汉字3个字符）;</li>
    <li>某些特殊字符(emoji表情符号等)是4字节的utf8编码;</li>
    <li>MYSQL在遇到超过最大字节范围的字符时，会忽略其后面的字符串，从而导致内容丢失。</li>
</ul>

##解决思路
<ul>
    <li>升级MYSQL到5.5.3版本以上，并将字段的编码设置为utf8mb4类型；</li>
    <li>通过程序，将字符串中4字节的utf8字符替换或者删除即可。</li>
</ul>

##剔除UTF8字符串中4字节的字符方法

    function removeByte4($str){
	    return preg_replace('/[\xF0-\xF7].../s','', $str);
    }

###性能
台式机，4.6W字符，0.006s，对程序性能影响基本可以忽略。

##扩展研究(其他的一些相关知识)

###问题底层原因

该问题是mysql本身的一个bug，可参考["Incorrect string value" error (1366) when inserting special characters“](http://bugs.mysql.com/bug.php?id=30131)

###UTF8 4字节各系统的支持情况
<ul>
    <li>windows xp: xp系统不支持4字节utf8字符, 浏览器用占位符显示;</li>
    <li>windows 7: 支持4字节utf8字符;</li>
    <li>mac os x: 支持4字节utf8字符;</li>
    <li>iPhone/iPad: 支持4字节utf8字符;</li>
</ul>

###如何支持手机上的表情

此问题可参考[mysql utf8mb4与emoji表情](http://my.oschina.net/wingyiu/blog/153357)。

<ul>
    <li>将特殊的4字节字符用相应的图片表情代替;</li>
    <li>替换的时候，注意不要堵塞用户的处理流程，当文本内容较长时，这是一个很耗性能的处理，建议先忽略4字节字符，然后将内容存到文件或者其他地方，后续使用脚本对这些内容进行替换处理等操作。;</li>
    <li>新浪微博等有做相应处理;</li>
    <li>特殊符合以及对应表情可参考[网站](http://www.charbase.com/);</li>
</ul>

###四种不同剔除utf8字符串中4字节字符的性能比对

测试机：普通pc；
测试文本：4.6W字节的中英文、特殊字符等混合；

####极快，推荐使用：0.006s

    function removeByte4($str){
	    return preg_replace('/[\xF0-\xF7].../s','', $str);
    }

####较慢，参考用：0.2s

    function removeByte4_2($str){
	    return preg_replace('/[\x{10000}-\x{10FFFF}]/u','', $str);
    }
  
####慢，但方便对特殊字符做不同替换：3s

    function removeByte4_1($str){//移除utf-8编码中4字节及以上的字符
    	mb_internal_encoding("UTF-8");
    	$len = mb_strlen($str);
    	
    	$res = '';
    	for($i=0;$i<$len;$i++){
    		$t = mb_substr($str,$i,1);
    		$res .= strlen($t)<=3?$t:'';
    	}
    	return $res;
    }

####极慢，不具使用价值，仅参考：10.9s

    function remove_4_byte($string) {
        $char_array = preg_split('/(?<!^)(?!$)/u', $string );
    	$len = count($char_array);
        for($x=0;$x<$len;$x++) {
            if(strlen($char_array[$x])>3) {
                $char_array[$x] = "";
            }
        }
        return implode($char_array, "");
    }

##结语

以上是对近期工作中碰到的一个MYSQL插入异常的原因及解决方法分析，分享给大家，转载请注明[出处:Vien Dave's Blog](http://daiweiyang.com/mysql_data_truncated/)。
