## iCalendar格式的農曆 節氣 及傳統節日

iCalendar是一種通用的日曆交換格式，很多軟體和裝置，比如google calendar, apple
calendar, thunderbird + lightning外掛, iphone/ipad, 安卓都支援。

以前訂過iCalendar格式農曆日曆，但慢慢地它們都停止了更新。所幸香港天文臺為公眾提
供了從1901年到2100年間兩百年的農曆-公曆對照表，也就是這裡用到的數據。

![Screenshot][]

下面是覆蓋去年、今年以及明年三年的日曆[ics檔案][iCal]鏈接，把它加入到你最常用的
軟體就可以了。

[https://raw.github.com/infinet/lunar-calendar/master/chinese_lunar_prev_year_next_year.ics][iCal]

蘋果裝置上應該是:
    設定 => 郵件、通訊錄、日曆 => 新增賬戶 => 其它 => 日曆 新增已訂閱日曆

如果在Mac的*iCal*里訂閱到iCloud，這個日曆還可以自動推送到所有使用那個iCloud
賬戶的ios裝置。


### 系統要求

 * Python: python 3.5上測試可用

 * [Numpy][] 和 [Numexpr][]: 純Python速度較慢，而天文演算法特別是完全版的VSOP87和LEA-406計算量尤其大，所以最好配合Numpy以加快計算速度。


### 產生更長時段農曆

如果需要更長時段的農曆，請先克隆本專案。

直接執行`./lunar_ical.py`會從香港天文臺抓取1901到2100年間所有數據，然後產生上面
那個前後三年時段的農曆ics檔案；

使用參數--start和--end指定需要的起至日期, 例如:

    ./lunar_ical.py --start=2010-05-01 --end=2021-12-31

超出1901-2100的農曆數據使用VSOP87行星理論和LEA-406月球理論產生. 以香港天文臺的
數據(更新于2014年)為標準，用此法產生的1949到2100年間農曆有兩處不一致：

 1）1979-01-20 大寒

 2）2057-09-29 農曆九月全部日期錯位一天

不一致的原因在於上面兩處節氣及新月正好跨越午夜時分，差距數秒就能影響該節氣或新月的
發生日期。由於使用不同的行星位置計算方法和Delta T估算方法，出現這種差異在所難免。


### C 版本

C版本速度更快，但暫時只在終端上輸出ical。使用者必須使用`>`將stdout的結果匯入到指定檔案以產生ical檔案。

編譯：

    $ cd c
    $ make

執行:

    #產生某年農曆
    $ ./lunarcal 2016 > chinese_lunar_2016.ics

    #產生數年農曆
    $ ./lunarcal 2016 2019 > chinese_lunar_2016_2019.ics

### 版權

本專案版權使用BSD協議，請參見所附COPYRIGHT檔案。

感謝[香港天文臺][HK_Obs]為公眾提供並授權本專案使用其農曆-公曆對照數據，該部分數據僅限非商業用途。



## Chinese Lunar Calendar

Google, Apple, and Microsoft used to provide Chinese Lunar Calendar in iCalendar
format, but most links were died over years. It is become hard to find a usable
Chinese Lunar Calendar for use with online and offline calendar apps.

The Chinese Lunar Calendar is mostly based on the motion of the Moon. It is
said due to the complicate interaction, mostly from the Sun and the Earth, the
motion of Moon is very hard to predict, especially on the long run.  Luckily
[Hong Kong Observatory][HK_Obs] has published a conversion table for the period
from 1901 to 2100. It is the most trustworthy Lunar Calendar I can find on the
web so far.

Lunar calendar beyond 1901-2100 range is generated by finding solar terms and
moon phases uses [VSOP87](ftp://ftp.imcce.fr/pub/ephem/planets/vsop87) planetary
theory and [LEA-406][] lunar theory.

The full version of LEA-406 and VSOP87 are used by default. A truncated version
, aa.py, which is slightly faster, is also included. The accuracy of this
implementation, when compare to the apparent Sun and Moon longitude finded by
the DE431 based JPL Horizon, are showed in following figures:

![aa_full][]
![aa_trunc][]

The lunar calendar generated by the full and truncated version is identical for
the period 1949 - 2100.  There are only two discrepancies compare to the HKO's
version: one is a solar term on 1979-01-20, the other is a new moon on
2057-09-29. It is caused by few seconds of error happens around midnight(UTC
+8).

The official timezone before 1949 is slightly different than the current UTC +8
therefor the computed lunar calendar may not represent history accurately.


### License

This package is released under the terms and conditions of the BSD License, a
copy is include in the file COPYRIGHT.

**Hong Kong Observatory** has been very kind to provide and grant the permission
of using their conversion table, which is only for Non-Commercial use.


### Requirement:

 * Python: tested on python 3.5

 * [Numpy][] and [Numexpr][]: Only needed when generate calendar by astronomical
  algorithm. The full version of LEA-406 and VSOP87 is rather slow when compute
  in pure python.


### How to run

Run `lunar_ical`, it will fetch data from Hong Kong Observatory, save
the data to a local sqlite database, then use that database to generate a ics
file, which covers from the previous to the end of next year.

Try the Chinese Lunar Calendar by add this [ics file][iCal] to your favorite
calendar app.

Start and end date can also be specified as command line options, for example:

    ./lunar_ical.py --start=1990-01-01 --end=2001-01-01

The date must in ISO format.


### C port

There is also a C version under directory "c". Run `make` to generate the
executable `lunarcal`. Run `lunarcal` with year will print out the Chinese Lunar
Calendar in ical format to the terminal. Use `>` to redirect the output to a file, for example:

    # Specific year
    $ ./lunarcal 2016 > chinese_lunar_2016.ics

    # or multiple years
    $ ./lunarcal 2016 2019 > chinese_lunar_2016_2019.ics


[Contact me](mailto: weichen302@gmail.com)

[iCal]: https://raw.github.com/infinet/lunar-calendar/master/chinese_lunar_prev_year_next_year.ics
[HK_Obs]: http://data.weather.gov.hk/gts/time/conversion1_text_c.htm
[Screenshot]: http://infinet.github.io/images/lunar_calendar.jpg
[aa_full]: http://infinet.github.io/images/moon-sun-full_lea406_vsop87_compare_JPL.png
[aa_trunc]: http://infinet.github.io/images/moon-sun-trunc_lea406_vsop87_compare_JPL.png
[Numpy]: http://www.numpy.org
[Numexpr]: https://github.com/pydata/numexpr
[LEA-406]: http://www.aanda.org/articles/aa/full/2007/33/aa7568-07/aa7568-07.html
