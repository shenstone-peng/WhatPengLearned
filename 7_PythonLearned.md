# 6 python
## 6.1 正则表达式
```python
#!/usr/bin/python
# coding=utf-8
import os
import shutil
import sys
import re

result = re.match(reg_pattern,Astr)
if result is None:
    print 1
print result.group(1)
print result.group(3)
print result.group(4)
spfd=open('data.csv', mode='a')
with open('sp.txt') as f:
    for line in f:
        line=line.strip('\n')
        reg_pattern = r'.*BusinessSessionID:<(.*)>.*RTSPCreateLinkTime:<(.*)>.*DescribeRequestTime:<(.*) ms>.*DescribeResonseTime:<(.*) ms>.*SetupRequestTime:<(.*) ms>.*SetupResponseTime:<(.*) ms>    .*PlayRequesetTime:<(.*) ms>.*PlayResponseTime:<(.*) ms>.*FirstPackTime:<(.*) ms>.*Pat/PmtTime:<(.*) ms>.*I-Frame-StartTime:<(.*) ms>.*IFrame-TailTime:<(.*) ms>.*TotalTime:<(.*) ms>.*'
        result = re.match(reg_pattern,line)
        data=result.group(1)
        for i in range(2,14):
            data = data+','+result.group(i)
        data = data +'\n'
        spfd.write(data)
   ```
