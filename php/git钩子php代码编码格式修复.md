Title: git钩子php代码编码格式修复
Date: 2017-12-19 13:45
Category: 其他

>以下是钩子脚本，放在.git/hooks/pre-commit

```python
#! /usr/bin/env python
# coding=utf-8
""" 代码提交前实现自动进行php-cs-fix """

import os
import sys
import subprocess


def php_cs_fix():

    # 获取有改动的文档
    staged_cmd = 'git status --porcelain=v1'
    proc = subprocess.Popen(staged_cmd, shell=True, stdout=subprocess.PIPE)
    with proc.stdout as std_out:
        for staged_file in std_out.readlines():
            staged_file = staged_file.strip()
            if staged_file.split()[0] not in ('M', '??'):
                continue
            else:
                if is_php_file(staged_file.split()[1]):
                    subprocess.call(
                        'php-cs-fixer fix --show-progress=estimating %s' % staged_file.split()[1],
                        shell=True)
        pass
    return True


def is_php_file(filename):
    if filename.endswith('.php') and os.path.exists(filename):
        return True

    return False


print '=================== php fix start ======================='
done = php_cs_fix()
print '=================== php fix end ========================='
sys.exit(0 if done else 1)
```