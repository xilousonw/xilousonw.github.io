1 在hexo g hexo d后 CNAME文件不见了导致网站404：

需要重新git push一下

```git
git init
git remote add origin https://github.com/xilousonw/xilousonw.github.io
git add .
git commit -m "Initial commit"
git push -u origin master
```

2.在执行

```git	
git remote add origin https://github.com/xilousonw/xilousonw.github.io
```

后，出现

```	git	
fatal: remote origin already exists.
```

这事由于目标已经存在

需要执行

```git	
git remote rm origin
然后重新执行
git remote add origin https://github.com/xilousonw/xilousonw.github.io
```



3.如果在执行git push之后出现

```errors
To https://github.com/xilousonw/xilousonw.github.io
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://github.com/xilousonw/xilousonw.github.io'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

那么只需要执行

```git	
git pull --rebase origin master
```

然后重新push即可