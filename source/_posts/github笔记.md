---
title: github笔记
date: 2022-04-13 21:29:13
tags: github
---

一：github使用

<!-- more -->		

​		创建项目，远程联系仓库，提交文件
​		git commit -m '初始化项目' 
​		git status   =》 查看项目的状态
​		git push  =》提交文件到仓库
​		

		下面的两条是直接在项目中和远程仓库来直接连接，以后直接push，不需要再commit
		git remote add origin https://github.com/liutaoDALAO/testmall.git
		it push -u origin main   // 这条命令，因为现在是main文件夹，以前是master，但是这里要把main换成master才能提交成功