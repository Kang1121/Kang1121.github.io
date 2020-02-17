---
title: Image.convert转化图片
date: 2019-12-20 20:19:34
tags:
categories:
- Deep Learning
---

```reStructuredText
 1 ------------------（1位像素，黑白，每字节一个像素存储）
 L ------------------（8位像素，黑白）
 P ------------------（8位像素，使用调色板映射到任何其他模式）
 RGB-------------- （3x8位像素，真彩色）
 RGBA-------------（4x8位像素，带透明度掩模的真彩色）
 CMYK-------------（4x8位像素，分色）
 YCbCr------------ （3x8位像素，彩色视频格式）
 I--------------------（32位有符号整数像素）
 F------------------- （32位浮点像素）
```



```python
img = Image.open(path).convert('1')
```



<div  align="center">    
 <img src="/images/Image.convert转化图片/a.png" width = "320" height = "240" align=center />
</div>



```python
img = Image.open(path).convert('L')
```

<div  align="center">    
 <img src="/images/Image.convert转化图片/b.png" width = "320" height = "240" align=center />
</div>

```python
img = Image.open(path).convert('P')
```

<div  align="center">    
 <img src="/images/Image.convert转化图片/c.png" width = "320" height = "240" align=center />
</div>

```
img = Image.open(path).convert('RGB')
```

<div  align="center">    
 <img src="/images/Image.convert转化图片/d.png" width = "320" height = "240" align=center />
</div>

```
img = Image.open(path).convert('RGBA')
```

<div  align="center">    
 <img src="/images/Image.convert转化图片/e.png" width = "320" height = "240" align=center />
</div>

```python
img = Image.open(path).convert('CMYK')
```

<div  align="center">    
 <img src="/images/Image.convert转化图片/f.png" width = "320" height = "240" align=center />
</div>

```python
img = Image.open(path).convert('YCbCr')
```

<div  align="center">    
 <img src="/images/Image.convert转化图片/g.png" width = "320" height = "240" align=center />
</div>

```python
img = Image.open(path).convert('I')
```

<div  align="center">    
 <img src="/images/Image.convert转化图片/h.png" width = "320" height = "240" align=center />
</div>

```python
img = Image.open(path).convert('F')
```

<div  align="center">    
 <img src="/images/Image.convert转化图片/k.png" width = "320" height = "240" align=center />
</div>