###1.安装node node-v8.6.0-x64.msi
###2.安装git Git-2.8.1-64-bit.exe
###3.git中安装cnpm 
 npm install -g cnpm --registry=https://registry.npm.taobao.org
###4.安装webstorm
在git中安装cnpm
打开webstorm后，
项目运行使用：npm run dev
###新建项目
1.在项目文件夹中打开git bash
2.输入命令： vue init webpack object02
3.先新建一个node_modules空文件夹，
4.使用webstorm打开新建的项目，忽略这个文件夹，
5.安装所有包：cnpm install
6.注意：在路由中选择y。其他直接回车，如果没有选就cnpm install 包名 --save安装
7.运行项目：npm run dev
7.克隆一个项目 git clone url