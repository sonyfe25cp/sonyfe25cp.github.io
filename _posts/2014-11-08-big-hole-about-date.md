---
layout: post
title: "DateUtils的深坑"
category: java

---

###日期转换

static Date parseFromStr(String dateStr) {
  if (dateStr != null) {
    java.util.Date date_s = null;
    try {
      //dateStr = "2009-02"
      date_s = DateUtils.parseDate(dateStr, new String[]{"yyyyMM", "yyyy", "yyyy-MM"});
      logger.info("s =====>> {} -> {}", dateStr, date_s.toString());
      java.util.Date date_s1 = DateUtils.parseDate(dateStr, new String[]{"yyyy-MM"});
      logger.info("s1 ====>> {} -> {}", dateStr, date_s1.toString());
      java.util.Date date_s2 = DateUtils.parseDate(dateStr, new String[]{"yyyyMM"});
      logger.info("s2 ====>> {} -> {}", dateStr, date_s2.toString());
    } catch (ParseException e) {
      e.printStackTrace();
    }
    Date result = new Date(date_s.getTime());
    logger.info("{} ==> {}", dateStr, result.toString());
    return result;
  } else {
    return null;
  }
}

###输入:2009-02

###输出结果

s =====>> 2009-02 -> Wed Oct 01 00:00:00 CST 2008
s1 ====>> 2009-02 -> Sun Feb 01 00:00:00 CST 2009
s2 ====>> 2009-02 -> Wed Oct 01 00:00:00 CST 2008
2009-02 ==> 2008-10-01

问题处在于 2009-02 在经过 yyyyMM 这个格式的时候，会被解析成 2008-10。

###解决办法

把这一行代码
      date_s = DateUtils.parseDate(dateStr, new String[]{"yyyyMM", "yyyy", "yyyy-MM"});
调整成为：
      date_s = DateUtils.parseDate(dateStr, new String[]{"yyyy-MM", "yyyy", "yyyyMM"});

###正确的输出

s =====>> 2009-02 -> Sun Feb 01 00:00:00 CST 2009
s1 ====>> 2009-02 -> Sun Feb 01 00:00:00 CST 2009
s2 ====>> 2009-02 -> Wed Oct 01 00:00:00 CST 2008
2009-02 ==> 2009-02-01


apache的包居然有这种坑，也是醉了。
