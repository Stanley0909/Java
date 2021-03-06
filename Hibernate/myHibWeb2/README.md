﻿<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>【框架】[Hibernate]利用Hibernate进行一对多的级联操作-Web实例</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><blockquote cite="陈浩翔">
<p>转载请注明出处：<a href="http://blog.csdn.net/qq_26525215"><font color="green">http://blog.csdn.net/qq_26525215</font></a><br><br>
本文源自<strong>【<a href="http://blog.csdn.net/qq_26525215" target="_blank">大学之旅_谙忆的博客</a>】</strong></p>
</blockquote>

<p>准备两个表，学生表，和学院表，它们的关系是一对多，一个学生对应一个学院，一个学院可以对应多个学生。 <br>
在此： <br>
1、演示利用一对多关系进行级联查询，也就是，只查询某个学院，同时将学院中的所有学生查询出来。 <br>
2、演示利用一对多关系进行级联存储，也就是说，只存储学院，但是同时将学生信息存储进学生表。</p>



<h1 id="准备的数据库数据">准备的数据库数据:</h1>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">database</span> hib charset=utf8;</span>
use hib;
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">table</span> students(
    sId <span class="hljs-keyword">varchar</span>(<span class="hljs-number">8</span>) <span class="hljs-keyword">primary</span> <span class="hljs-keyword">key</span>,
    sName <span class="hljs-keyword">varchar</span>(<span class="hljs-number">40</span>),
    sAge <span class="hljs-keyword">int</span>,
    deptId <span class="hljs-keyword">varchar</span>(<span class="hljs-number">8</span>)
);</span>

<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-keyword">table</span> depts(
  dId <span class="hljs-keyword">varchar</span>(<span class="hljs-number">8</span>) <span class="hljs-keyword">primary</span> <span class="hljs-keyword">key</span>,
  dName <span class="hljs-keyword">varchar</span>(<span class="hljs-number">40</span>)
);</span>

<span class="hljs-operator"><span class="hljs-keyword">alter</span> <span class="hljs-keyword">table</span> students <span class="hljs-keyword">add</span>(<span class="hljs-keyword">constraint</span> fk_stu_dept <span class="hljs-keyword">foreign</span> <span class="hljs-keyword">key</span>(deptId) <span class="hljs-keyword">references</span> dept(dId));</span>

<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> depts(dId,dName) <span class="hljs-keyword">values</span>(<span class="hljs-string">'D001'</span>,<span class="hljs-string">'信息科学与工程学院'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> depts(dId,dName) <span class="hljs-keyword">values</span>(<span class="hljs-string">'D002'</span>,<span class="hljs-string">'土木工程学院'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> depts(dId,dName) <span class="hljs-keyword">values</span>(<span class="hljs-string">'D003'</span>,<span class="hljs-string">'数学与计算机学院'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> depts(dId,dName) <span class="hljs-keyword">values</span>(<span class="hljs-string">'D004'</span>,<span class="hljs-string">'通信学院'</span>);</span>

<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> students(sId,sName,sAge,deptId) <span class="hljs-keyword">values</span>(<span class="hljs-string">'S001'</span>,<span class="hljs-string">'Jack'</span>,<span class="hljs-number">23</span>,<span class="hljs-string">'D001'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> students(sId,sName,sAge,deptId) <span class="hljs-keyword">values</span>(<span class="hljs-string">'S002'</span>,<span class="hljs-string">'Tom'</span>,<span class="hljs-number">25</span>,<span class="hljs-string">'D001'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> students(sId,sName,sAge,deptId) <span class="hljs-keyword">values</span>(<span class="hljs-string">'S003'</span>,<span class="hljs-string">'张三'</span>,<span class="hljs-number">43</span>,<span class="hljs-string">'D001'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> students(sId,sName,sAge,deptId) <span class="hljs-keyword">values</span>(<span class="hljs-string">'S004'</span>,<span class="hljs-string">'李四'</span>,<span class="hljs-number">55</span>,<span class="hljs-string">'D001'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> students(sId,sName,sAge,deptId) <span class="hljs-keyword">values</span>(<span class="hljs-string">'S005'</span>,<span class="hljs-string">'Jack'</span>,<span class="hljs-number">23</span>,<span class="hljs-string">'D002'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> students(sId,sName,sAge,deptId) <span class="hljs-keyword">values</span>(<span class="hljs-string">'S006'</span>,<span class="hljs-string">'Tom'</span>,<span class="hljs-number">25</span>,<span class="hljs-string">'D003'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> students(sId,sName,sAge,deptId) <span class="hljs-keyword">values</span>(<span class="hljs-string">'S007'</span>,<span class="hljs-string">'张三'</span>,<span class="hljs-number">43</span>,<span class="hljs-string">'D002'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">insert</span> <span class="hljs-keyword">into</span> students(sId,sName,sAge,deptId) <span class="hljs-keyword">values</span>(<span class="hljs-string">'S008'</span>,<span class="hljs-string">'李四'</span>,<span class="hljs-number">55</span>,<span class="hljs-string">'D002'</span>);</span></code></pre>

<p>students表数据如下:</p>

<p><img src="http://img.blog.csdn.net/20160830164627202" alt="" title=""></p>

<p>depts表数据如下:</p>

<p><img src="http://img.blog.csdn.net/20160830164657811" alt="" title=""></p>

<p>因为代码比较多，只演示部分代码，完整代码在后面会给出链接。 <br>
需要的JAR包，也全部在项目中，下载完整项目即可。</p>



<h1 id="indexjsp">index.jsp:</h1>



<pre class="prettyprint"><code class=" hljs xml"><span class="vbscript">&lt;%@ page language=<span class="hljs-string">"java"</span> import=<span class="hljs-string">"java.util.*"</span> pageEncoding=<span class="hljs-string">"UTF-8"</span>%&gt;</span>
<span class="vbscript">&lt;%@taglib uri=<span class="hljs-string">"http://java.sun.com/jsp/jstl/core"</span> prefix=<span class="hljs-string">"c"</span>%&gt;</span>

<span class="hljs-doctype">&lt;!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">html</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">head</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">title</span>&gt;</span>Hibernate中表之间的一对多关系<span class="hljs-tag">&lt;/<span class="hljs-title">title</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">style</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text/css"</span>&gt;</span><span class="css">
<span class="hljs-tag">table</span> <span class="hljs-rules">{
    <span class="hljs-rule"><span class="hljs-attribute">border</span>:<span class="hljs-value"> <span class="hljs-number">1</span>px solid gray</span></span>;
    <span class="hljs-rule"><span class="hljs-attribute">border-collapse</span>:<span class="hljs-value"> collapse</span></span>;
    <span class="hljs-rule"><span class="hljs-attribute">width</span>:<span class="hljs-value"> <span class="hljs-number">60</span>%</span></span>;
<span class="hljs-rule">}</span></span>

<span class="hljs-tag">td</span> <span class="hljs-rules">{
    <span class="hljs-rule"><span class="hljs-attribute">border</span>:<span class="hljs-value"> <span class="hljs-number">1</span>px solid gray</span></span>;
    <span class="hljs-rule"><span class="hljs-attribute">padding</span>:<span class="hljs-value"> <span class="hljs-number">5</span>px</span></span>;
<span class="hljs-rule">}</span></span>
</span><span class="hljs-tag">&lt;/<span class="hljs-title">style</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">head</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">body</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">h3</span>&gt;</span>通过学院id查询学院表，把该学院的学生信息也同时输出来<span class="hljs-tag">&lt;/<span class="hljs-title">h3</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">form</span> <span class="hljs-attribute">action</span>=<span class="hljs-value">"&lt;c:url value='/DemoServlet?cmd=queryDeptById'/&gt;"</span>
        <span class="hljs-attribute">method</span>=<span class="hljs-value">"post"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">table</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>学院编号<span class="hljs-tag">&lt;<span class="hljs-title">font</span> <span class="hljs-attribute">color</span>=<span class="hljs-value">"red"</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">font</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"deptId"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span> <span class="hljs-attribute">colspan</span>=<span class="hljs-value">"2"</span> <span class="hljs-attribute">align</span>=<span class="hljs-value">"center"</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"submit"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"查询"</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">table</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">form</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">c:if</span> <span class="hljs-attribute">test</span>=<span class="hljs-value">"${!empty sessionScope.map }"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">h3</span>&gt;</span>查询结果<span class="hljs-tag">&lt;/<span class="hljs-title">h3</span>&gt;</span>
          学院名称:${map.deptName}
          <span class="hljs-tag">&lt;<span class="hljs-title">table</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>学号<span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>姓名<span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>年龄<span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>学院编号<span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">c:forEach</span> <span class="hljs-attribute">items</span>=<span class="hljs-value">"${map.qlist}"</span> <span class="hljs-attribute">var</span>=<span class="hljs-value">"stud"</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>${stud.sId}<span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>${stud.sName}<span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>${stud.sAge}<span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>${stud.dept.dId}<span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">c:forEach</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">table</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">c:remove</span> <span class="hljs-attribute">var</span>=<span class="hljs-value">"map"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">c:if</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">h3</span>&gt;</span>添加学生/学院<span class="hljs-tag">&lt;/<span class="hljs-title">h3</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">form</span> <span class="hljs-attribute">action</span>=<span class="hljs-value">"&lt;c:url value='/DemoServlet?cmd=addDept'/&gt;"</span> <span class="hljs-attribute">method</span>=<span class="hljs-value">"post"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">table</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>学院编号<span class="hljs-tag">&lt;<span class="hljs-title">font</span> <span class="hljs-attribute">color</span>=<span class="hljs-value">"red"</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">font</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"deptId"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span>学院名称
                <span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"deptName"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span> <span class="hljs-attribute">align</span>=<span class="hljs-value">"center"</span>&gt;</span>学生学号<span class="hljs-tag">&lt;<span class="hljs-title">font</span> <span class="hljs-attribute">color</span>=<span class="hljs-value">"red"</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">font</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span> <span class="hljs-attribute">align</span>=<span class="hljs-value">"center"</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"studId"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span> <span class="hljs-attribute">align</span>=<span class="hljs-value">"center"</span>&gt;</span>学生姓名<span class="hljs-tag">&lt;<span class="hljs-title">font</span> <span class="hljs-attribute">color</span>=<span class="hljs-value">"red"</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">font</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span> <span class="hljs-attribute">align</span>=<span class="hljs-value">"center"</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"studName"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span> <span class="hljs-attribute">align</span>=<span class="hljs-value">"center"</span>&gt;</span>学生年龄<span class="hljs-tag">&lt;<span class="hljs-title">font</span> <span class="hljs-attribute">color</span>=<span class="hljs-value">"red"</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">font</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span> <span class="hljs-attribute">align</span>=<span class="hljs-value">"center"</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"studAge"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">tr</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">td</span> <span class="hljs-attribute">colspan</span>=<span class="hljs-value">"2"</span> <span class="hljs-attribute">align</span>=<span class="hljs-value">"center"</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"submit"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"存储"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">td</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">tr</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">table</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">form</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">body</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">html</span>&gt;</span>
</code></pre>



<h1 id="demojdbcdaojava">DemoJdbcDao.java</h1>

<p>数据层。</p>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.demo</span><span class="hljs-preprocessor">.dao</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.hibernate</span><span class="hljs-preprocessor">.Query</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.hibernate</span><span class="hljs-preprocessor">.Session</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.hibernate</span><span class="hljs-preprocessor">.Transaction</span><span class="hljs-comment">;</span>

import cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.domain</span><span class="hljs-preprocessor">.Dept</span><span class="hljs-comment">;</span>
import cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.domain</span><span class="hljs-preprocessor">.Student</span><span class="hljs-comment">;</span>
import cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.hib</span><span class="hljs-preprocessor">.HibernateSessionFactory</span><span class="hljs-comment">;</span>

public class DemoJdbcDao {
    public Dept queryDeptById(Dept dept) {
        Session s = HibernateSessionFactory<span class="hljs-preprocessor">.getSession</span>()<span class="hljs-comment">;</span>
        String hql = <span class="hljs-string">"from Dept d where d.dId=?"</span><span class="hljs-comment">;</span>
        //String hql = <span class="hljs-string">"from Dept"</span><span class="hljs-comment">;</span>
        Query query = s<span class="hljs-preprocessor">.createQuery</span>(hql)<span class="hljs-comment">;</span>
        query<span class="hljs-preprocessor">.setParameter</span>(<span class="hljs-number">0</span>, dept<span class="hljs-preprocessor">.getdId</span>())<span class="hljs-comment">;</span>
        //根据部门ID去查的，只会有一个查询结果
        Dept resDept = (Dept) query<span class="hljs-preprocessor">.list</span>()<span class="hljs-preprocessor">.get</span>(<span class="hljs-number">0</span>)<span class="hljs-comment">;</span>
        return resDept<span class="hljs-comment">;</span>
    }

    public void addDept(Dept dept) {
        Session s = HibernateSessionFactory<span class="hljs-preprocessor">.getSession</span>()<span class="hljs-comment">;</span>
        if(dept<span class="hljs-preprocessor">.getdName</span>()==null){
            Query query = s<span class="hljs-preprocessor">.createQuery</span>(<span class="hljs-string">"from Dept d where d.dId=?"</span>)<span class="hljs-comment">;</span>
            query<span class="hljs-preprocessor">.setParameter</span>(<span class="hljs-number">0</span>, dept<span class="hljs-preprocessor">.getdId</span>())<span class="hljs-comment">;</span>
            //对于学院存在的，如果没有填写学院名称，为其补上
            dept<span class="hljs-preprocessor">.setdName</span>( ((Dept) query<span class="hljs-preprocessor">.list</span>()<span class="hljs-preprocessor">.get</span>(<span class="hljs-number">0</span>))<span class="hljs-preprocessor">.getdName</span>() )<span class="hljs-comment">;</span>
        }
        s<span class="hljs-preprocessor">.clear</span>()<span class="hljs-comment">;//把之前的session信息清空，因为不允许一个session对象进行几个操作</span>
        Transaction tx = s<span class="hljs-preprocessor">.beginTransaction</span>()<span class="hljs-comment">;</span>
        s<span class="hljs-preprocessor">.saveOrUpdate</span>(dept)<span class="hljs-comment">;</span>
        tx<span class="hljs-preprocessor">.commit</span>()<span class="hljs-comment">;</span>
    }

}
</code></pre>



<h1 id="demoservlet">DemoServlet</h1>

<p>servlet层</p>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.demo</span><span class="hljs-comment">;</span>


import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.PrintWriter</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.HashMap</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.List</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Map</span><span class="hljs-comment">;</span>

import javax<span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HttpServletRequest</span><span class="hljs-comment">;</span>
import javax<span class="hljs-preprocessor">.servlet</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.HttpServletResponse</span><span class="hljs-comment">;</span>

import cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.demo</span><span class="hljs-preprocessor">.service</span><span class="hljs-preprocessor">.DemoServiceImpl</span><span class="hljs-comment">;</span>
import cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.domain</span><span class="hljs-preprocessor">.Dept</span><span class="hljs-comment">;</span>
import cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.domain</span><span class="hljs-preprocessor">.Student</span><span class="hljs-comment">;</span>
import cn<span class="hljs-preprocessor">.hncu</span><span class="hljs-preprocessor">.utils</span><span class="hljs-preprocessor">.BaseServlet</span><span class="hljs-comment">;</span>

public class DemoServlet extends BaseServlet {
    DemoServiceImpl service = new DemoServiceImpl()<span class="hljs-comment">;</span>
    @Override
    public void execute(HttpServletRequest req, HttpServletResponse resp)
            throws Exception {

    }

    public void queryDeptById(HttpServletRequest req, HttpServletResponse resp)
            throws Exception {
        String deptId = req<span class="hljs-preprocessor">.getParameter</span>(<span class="hljs-string">"deptId"</span>)<span class="hljs-comment">;</span>
        if(deptId==null||deptId<span class="hljs-preprocessor">.trim</span>()<span class="hljs-preprocessor">.length</span>()==<span class="hljs-number">0</span>){
            resp<span class="hljs-preprocessor">.sendRedirect</span>(getServletContext()<span class="hljs-preprocessor">.getContextPath</span>())<span class="hljs-comment">;</span>
            return<span class="hljs-comment">;</span>
        }
        Dept dept = new Dept()<span class="hljs-comment">;</span>
        dept<span class="hljs-preprocessor">.setdId</span>(deptId)<span class="hljs-comment">;</span>
        dept = service<span class="hljs-preprocessor">.queryDeptById</span>(dept)<span class="hljs-comment">;</span>
        Map&lt;String, Object&gt; map = new HashMap&lt;String, Object&gt;()<span class="hljs-comment">;</span>

        map<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"deptName"</span>, dept<span class="hljs-preprocessor">.getdName</span>())<span class="hljs-comment">;</span>
        map<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"qlist"</span>, dept<span class="hljs-preprocessor">.getStudents</span>())<span class="hljs-comment">;</span>

        req<span class="hljs-preprocessor">.getSession</span>()<span class="hljs-preprocessor">.setAttribute</span>(<span class="hljs-string">"map"</span>, map)<span class="hljs-comment">;</span>
        resp<span class="hljs-preprocessor">.sendRedirect</span>(getServletContext()<span class="hljs-preprocessor">.getContextPath</span>())<span class="hljs-comment">;</span>
    }

    public void addDept(HttpServletRequest req, HttpServletResponse resp)
            throws Exception {
        String deptId = req<span class="hljs-preprocessor">.getParameter</span>(<span class="hljs-string">"deptId"</span>)<span class="hljs-comment">;</span>
        String deptName = req<span class="hljs-preprocessor">.getParameter</span>(<span class="hljs-string">"deptName"</span>)<span class="hljs-comment">;</span>
        if(deptName==null||deptName<span class="hljs-preprocessor">.trim</span>()<span class="hljs-preprocessor">.equals</span>(<span class="hljs-string">""</span>)){
            deptName=null<span class="hljs-comment">;</span>
        }
        String studId = req<span class="hljs-preprocessor">.getParameter</span>(<span class="hljs-string">"studId"</span>)<span class="hljs-comment">;</span>
        String studName = req<span class="hljs-preprocessor">.getParameter</span>(<span class="hljs-string">"studName"</span>)<span class="hljs-comment">;</span>
        String studAge = req<span class="hljs-preprocessor">.getParameter</span>(<span class="hljs-string">"studAge"</span>)<span class="hljs-comment">;</span>
        int age = Integer<span class="hljs-preprocessor">.parseInt</span>(studAge)<span class="hljs-comment">;</span>

        Dept dept = new Dept()<span class="hljs-comment">;</span>
        dept<span class="hljs-preprocessor">.setdId</span>(deptId)<span class="hljs-comment">;</span>
        dept<span class="hljs-preprocessor">.setdName</span>(deptName)<span class="hljs-comment">;</span>

        Student s1 = new Student()<span class="hljs-comment">;</span>
        s1<span class="hljs-preprocessor">.setsId</span>(studId)<span class="hljs-comment">;</span>
        s1<span class="hljs-preprocessor">.setsName</span>(studName)<span class="hljs-comment">;</span>
        s1<span class="hljs-preprocessor">.setsAge</span>(age)<span class="hljs-comment">;</span>
        s1<span class="hljs-preprocessor">.setDept</span>(dept)<span class="hljs-comment">;//多方进行设置外键</span>

        //把多方添加到一方的集合中
        dept<span class="hljs-preprocessor">.getStudents</span>()<span class="hljs-preprocessor">.add</span>(s1)<span class="hljs-comment">;</span>

        service<span class="hljs-preprocessor">.addDept</span>(dept)<span class="hljs-comment">;</span>

        resp<span class="hljs-preprocessor">.sendRedirect</span>(getServletContext()<span class="hljs-preprocessor">.getContextPath</span>())<span class="hljs-comment">;</span>
    }

}
</code></pre>

<h1 id="演示结果">演示结果：</h1>

<p>开始显示页面:</p>

<p><img src="http://img.blog.csdn.net/20160830165721503" alt="" title=""></p>

<p>查询D002学院:</p>

<p><img src="http://img.blog.csdn.net/20160830165756394" alt="" title=""></p>

<p>为D002学院添加学生:</p>

<p><img src="http://img.blog.csdn.net/20160830170254542" alt="" title=""></p>

<p>结果: <br>
<img src="http://img.blog.csdn.net/20160830170313991" alt="" title=""></p>

<p>添加D005学院-科技学院</p>

<p><img src="http://img.blog.csdn.net/20160830170336746" alt="" title=""></p>

<p>结果</p>

<p><img src="http://img.blog.csdn.net/20160830170347992" alt="" title=""></p>



<h1 id="完整的项目链接">完整的项目链接:</h1>

<h1 id="小小的总结">小小的总结:</h1>

<p>此项目，我写的时候比较急，因为马上要学Spring框架了，有些方面没考虑到，有兴趣的可以自己取完善一下。例如，在增加学院和学生的时候，增加一个按钮，添加学生。再比如，把service,DAO层完善一下，写好接口，最好再写个过滤器，全站压缩，编码啥的。哈哈，自己可以加功能的。</p>

<blockquote cite="陈浩翔">
<p>转载请注明出处：<a href="http://blog.csdn.net/qq_26525215"><font color="green">http://blog.csdn.net/qq_26525215</font></a><br><br>
本文源自<strong>【<a href="http://blog.csdn.net/qq_26525215" target="_blank">大学之旅_谙忆的博客</a>】</strong></p>
</blockquote></div></body>
</html>