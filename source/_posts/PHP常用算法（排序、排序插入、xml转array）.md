layout: post
title: "PHP常用算法（排序、排序插入、xml转array）"
date: 2015-08-15 12:27:24
categories: [php]
tags: [php, 排序, 排序插入, xml转array]
keywords: php, 排序, 排序插入, xml转array
description: php, 排序, 排序插入, xml转array
comments: true
photos:
-
---
## 一、排序

#### ** 1、二维数组排序 **

主要思路是取出要用来的排序的字段，并以源数组键为建，对其进行相应排序后，对应回源数组。

{% codeblock lang:php %}
    /**
     * 二维数组排序（保留键、值）
     * @param  $arr       array     排序数组
     * @param  $keys      string    排序字段
     * @param  $order     int       排序依据 0正序，1逆序
     * @return $new_array array     返回排序后数组
     */
    function array_sort($arr,$keys,$order){
        //判断是否为数组，否则直接返回false
        if(!is_array($arr)){
            return false;
        }
        //定义一个存储排序字段的空数组
        $keysvalue = array();
        //遍历排序要排序的数组，将要排序的字段取出，形成一个一维数组，键为源数组键，值为排序字段值
        foreach($arr as $key=>$val){
            $keysvalue[$key] = $val[$keys];
        }
        //根据order参数，对上述以为数组排序，注意需采用asort以保留键名
        if($order == 0){
            asort($keysvalue);
        }else{
            arsort($keysvalue);
        }
        //将指针重置至第一个
        reset($keysvalue);
        //定一个空数组，用以保存排序后的数组
        $new_array = array();
        //遍历排序后的一维数组，由于其键就是源数组的键，对应其键就可形成一个新数组
        foreach($keysort as $key=>$val){
            $new_array[$key] = $arr[$key];
        }
        //返回排序后的新数组
        return $new_array;
    }
{% endcodeblock %}

#### ** 2、快速排序 **

前段时间遇到一个需要根据二位数组的中的多个维度进行排序的情况，于是将快速排序进行了一点改造，以支持多个维度的排序。
<!--more-->
{% codeblock lang:php %}
    /**
     * 快速排序(最多支持三个维度数据，可根据需要添加elseif语句，来扩充维度)
     * @param  $arr       array     排序数组 eg:$array = [[300,30,3],[300,40,2],[100,10,1],[100,10,2]];
     * @param  $order     int       排序依据 0正序，1逆序
     * @return $new_array array     返回排序后数组
     */
    function quickSort($arr){
        //判断数组是否需要排序
        if (count($arr) <= 1) {
            return $arr;
        }
        //获取数组首项作为对比基数
        $key = $arr[0];
        //定义左侧空数组
        $left_arr = array();
        //定义右侧空数组
        $right_arr = array();
        //对比基数，分别写入左右数组
        for ($i = 1; $i < count($arr); $i++) {
            if ($arr[$i] < $key) {
                    $left_arr[] = $arr[$i];
            } elseif ($arr[$i][0] < $key[0]) {
                $left_arr[] = $arr[$i];
            } elseif ($arr[$i] == $key) {
                if ($arr[$i][1] < $key[1]) {
                    $left_arr[] = $arr[$i];
                } elseif ($arr[$i][1] == $key[1]) {
                    if ($arr[$i][2] < $key[2]) {
                        $left_arr[] = $arr[$i];
                    }
                }
            } else {
                $right_arr[] = $arr[$i];
            }
        }
        //递归调用，对左右数组排序
        $left_arr = quickSort($left_arr);
        $right_arr = quickSort($right_arr);
        //合并并返回排序后的数组
        return array_merge($left_arr,array($key),$right_arr);
    }
{% endcodeblock %}

## 二、排序插入

同样是由于业务需要，在二分查找的基础上做了一个排序插入。
这个函数分两种情况：
一是如果插入的数据已经存在于当前数组，则不会重复插入，而是返回其所在位置。
二是如果插入的数据不存在则会将其插入顺序所在位置，并返回整个数组和其插入的位置，例如：向数组`[1, 2, 3, 5]`中插入`4`，则会返回`[1, 2, 3, 4, 5, 'current'=>3]`这样的一个数组

{% codeblock lang:php %}
/*
 * 排序插入 兼容 二分查找
 * @param   $array          array            Y 数组
 * @param   $v              string           Y 要找的值
 * @param   $low            int              N 查找范围的最小键值
 * @param   $high           int              N 范围的最大键值
 * @return  $array || $mid  array || int     如果不存在返回插入且已排序数组 || 如果存在返回所在位置
 */
function binInsert($array,$v,$low=0,$high=0){
    //判断是否第一次调用
    if($high == 0){
        $high = count($array);
        //排除首项相等
        if($array[0] == $v){
            return 0;
        }
    }
    //结束递归并插入
    $diff = $high - $low;
    if($diff == 0 || $diff == 1){
        array_splice($array, $low+1, 0, $v);
        //排除空数组首项
        if($diff == 0){
            $array['current'] = $low;
        }else{
            $array['current'] = $low + 1;
        }
        return $array;
    }
    //如果还存在剩余数组元素
    if($low<$high){
        //取$low和$high的中间值
        $mid = intval(($low+$high)/2);
        //比对并递归
        if($v == $array[$mid]){
            return $mid;
        }elseif($v < $array[$mid]){
            return binInsert($array, $v, $low, $mid);
        }else{
            return binInsert($array, $v, $mid, $high);
        }
    }
    return 1;
}

{% endcodeblock %}

## 三、xml 转 array
同样是在工作中遇到的一个问题，就是从第三方取来一组数据，是xml格式的，处理起来头疼的很，于是将其转换为array，就可以随便处理了。其核心部分就是正则表示式`/<(\w+)[^>]*>([\\x00-\\xFF]*?)<\\/\\1>/`。解释一下，`<(\w+)[^>]*>`这一个部分匹配标签头；`([\\x00-\\xFF]*?)`这一部分匹配标签内部所有内容，并且采用懒惰获取模式，即对后面的匹配模式`<\\/\\1>`预查后匹配，否则会匹配到所有后面的元素；`<\\/\\1>`这一部分匹配标签尾。

这其中有两部分匹配获取，及括号内的匹配项，会在下文的匹配结果（debug）中体现出来。

{% codeblock lang:php %}
    /**
     * xml数据转换为array
     * @param  $xml          xml      XML格式数据
     * @param  $repeatField  string   同级重复标签项
     * @return $arr          array    转换后的数组
     */
    function xml_to_array($xml, $repeatField){
        //xml数据预处理，去除空白字符
        $regs = "/\s*/";
        $xml = preg_replace($regs, '', $xml);
        //匹配标签及内容
        $reg = "/<(\w+)[^>]*>([\\x00-\\xFF]*?)<\\/\\1>/";
        if(preg_match_all($reg, $xml, $matches))
        {
            //1、获取匹配后的结果，参考下面的debug
            $count = count($matches[0]);
            for($i = 0; $i < $count; $i++)
            {
                $subxml= $matches[2][$i];
                //替换重复的标签项，否则会造成覆盖，如下文的TempTable，像这种重复的键就要用数字来代替
                if ($matches[1][$i] == $repeatField) {
                    $key = $i;
                } else {
                    $key = $matches[1][$i];
                }
                //判断是否subxml中的数据是否仍为xml数据
                if(preg_match($reg, $subxml))
                {  //是，递归
                    $arr[$key] = xml_to_array($subxml, $repeatField);
                }else{ //否直接写入
                    $arr[$key] = $subxml;
                }
            }
        }
        return $arr;
    }
{% endcodeblock %}

** 调用方法： **xml_to_array($xml, 'TempTable')
** 数据源： **参考如下

{% codeblock lang:xml %}
    <NewDataSet>
      <TempTable>
        <nID>2880</nID>
        <EnterpriseID>8088</EnterpriseID>
        <EnterNO>100158</EnterNO>
        <CallID>136****6155201506012258055571</CallID>
        <CallerID>136****6155</CallerID>
      </TempTable>
      <TempTable>
        <nID>2879</nID>
        <EnterpriseID>8023</EnterpriseID>
        <EnterNO>101658</EnterNO>
        <CallID>186****5873201506012012599627</CallID>
        <CallerID>186****5873</CallerID>
      </TempTable>
    </NewDataSet>
    <Flag>Ok</Flag>
    <ReturnMsg>获取呼入记录成功！</ReturnMsg>
{% endcodeblock %}

** debug 数据**

`array[0]`是元数据，`array[1]`是获取匹配一`(\w+)`获取的内容，`array[2]`是获取匹配二`([\\x00-\\xFF]*?)`获取的内容。

如此一层层递归下去，一层层剥掉她们的外衣！！！

```
array (size=3)
  0 =>
    array (size=3)
      0 => string '<NewDataSet>
                                <TempTable>
                                    <nID>2880</nID>
                                    <EnterpriseID>8088</EnterpriseID>
                                    <EnterNO>100158</EnterNO>
                                    <CallID>136****6155201506012258055571</CallID>
                                    <CallerID>136****6155</CallerID>
                                </TempTable>
                                <TempTable>
                                    <nID>2879</nID>
                                    <EnterpriseID>8023</EnterpriseID>
                                    <EnterNO>101658</EnterNO>
                                    <CallID>186****5873201506012012599627</CallID>
                                    <CallerID>186****5873</CallerID>
                                    </TempTable>
                                </NewDataSet>' (length=373)
      1 => string '<Flag>Ok</Flag>' (length=15)
      2 => string '<ReturnMsg>获取呼入记录成功！</ReturnMsg>' (length=50)
  1 =>
    array (size=3)
      0 => string 'NewDataSet' (length=10)
      1 => string 'Flag' (length=4)
      2 => string 'ReturnMsg' (length=9)
  2 =>
    array (size=3)
      0 => string '<TempTable>
                                <nID>2880</nID>
                                <EnterpriseID>8088</EnterpriseID>
                                <EnterNO>100158</EnterNO>
                                <CallID>136****6155201506012258055571</CallID>
                                <CallerID>136****6155</CallerID>
                            </TempTable>
                            <TempTable>
                                <nID>2879</nID>
                                <EnterpriseID>8023</EnterpriseID>
                                <EnterNO>101658</EnterNO>
                                <CallID>186****5873201506012012599627</CallID>
                                <CallerID>186****5873</CallerID>
                            </TempTable>' (length=348)
      1 => string 'Ok' (length=2)
      2 => string '获取呼入记录成功！' (length=27)
```

** 最终结果 **


```
    array (size=3)
      'NewDataSet' =>
        array (size=2)
          0 =>
            array (size=5)
              'nID' => string '2880' (length=4)
              'EnterpriseID' => string '8088' (length=4)
              'EnterNO' => string '100158' (length=6)
              'CallID' => string '136****6155201506012258055571' (length=29)
              'CallerID' => string '136****6155' (length=11)
          1 =>
            array (size=5)
              'nID' => string '2879' (length=4)
              'EnterpriseID' => string '8023' (length=4)
              'EnterNO' => string '101658' (length=6)
              'CallID' => string '186****5873201506012012599627' (length=29)
              'CallerID' => string '186****5873' (length=11)
      'Flag' => string 'Ok' (length=2)
      'ReturnMsg' => string '获取呼入记录成功！' (length=27)
  ```
