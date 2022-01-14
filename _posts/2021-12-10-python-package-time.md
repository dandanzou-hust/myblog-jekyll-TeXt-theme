---
title:  "【技术分享】python包:time"
date:   2021-12-10 12:00:00 +0800

categories: 技术分享
tag: "python包"

---

# Time包

time.gmtime( t )      1234915418  -> time.struct_time() 

time.mktime( t )      time.struct_time()   -> 1234915418

time.strftime("%Y-%m-%d %H:%M:%S", t)    time.struct_time() -> '2021-08-12 22:34:25'

time.strptime(t, "%Y-%m-%d %H:%M:%S")   '2021-08-12 22:34:25'  ->  time.struct_time()

待后续补充
