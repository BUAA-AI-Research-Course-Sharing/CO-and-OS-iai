# 内存管理

**1**
$$
2^{10}\times2^{10}\times4096B=2^{32}B
$$
**2**

第一级页表页目录项数目：
$$
2^{10}
$$
第二级页表页表项数目：
$$
2^{10}\times2^{10}=2^{20}
$$
**3**

一级页表的起始逻辑地址：
$$
0x1FC00000+(0x1FC00000>>12)<<2=0x1FC7F000
$$
0x01234567对应的页目录项的逻辑地址：
$$
0x1FC7F000+(0x01234567>>22)<<2=0x1FC7F010
$$
**4**
$$
0x1FC7F000+(0x1FC7F000>>22)<<2=0x1FC7F1FC
$$
**5**

该逻辑地址对应的页表项的物理地址：
$$
0x00007000+((0x12345678\&0x003FF000)>>12)<<2=0x00007D14
$$
该逻辑地址对应的页目录项中包含的物理页框号：
$$
0x00007000<<12=0x07000000
$$
取其高20位即得物理页框号：0x07000

# 进程同步与互斥

```
Semaphore vulcan = 10	\\缓存区空位数
Semaphore mutex = 1		\\访问缓存区的互斥信号量
Semaphore x = 0			\\缓存区X零件数目
Semaphore y = 0			\\缓存区Y零件数目
Semaphore z = 0			\\缓存区Z零件数目
Semaphore nx = 1		\\若为1,缓存区中没有x
Semaphore ny = 1		\\若为1,缓存区中没有y

main(){
	cobegin{
		R1();
		R2();
		R3();
		R4();
	}coend
}

R1()
{
	while(true)
	{
		produceX();
		P(nx);
		P(vulcan);
		P(mutex);
		putX();
		V(mutex);
		V(x);
	}
}

R2()
{
	while(true)
	{
		produceY();
		P(ny);
		P(vulcan);
		P(mutex);
		putY();
		V(mutex);
		V(y);
	}
}

R3()
{
	while(true)
	{
		P(x);
		P(y);
		P(mutex);
		getX();
		getY();
		V(mutex);
		V(vulcan);
		V(vulcan);
		V(nx);
		V(ny);
		produceZ();
		P(vulcan);
		P(mutex);
		putZ();
		V(mutex);
		V(z);
	}
}

R4()
{
	while(true)
	{
		P(z);
		P(mutex);
		getZ();
		V(mutex);
		V(vulcan);
		produceP();
	}
}
```

