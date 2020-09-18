# intellectual-data
## hivestudy
sql
```
select t1.*,t1.out_num+t1.mid_num+t1.in_num
,'cre','tm'
from(
select period_id, week_d, mon_d, qua_d, year_d
	, series, model, used_du, user_type, rom_type
	, sum(out_num) as out_num, sum(mid_num) as mid_num
	, sum(in_num) as in_num
from oppo_daas_dev_dw.dwd_qty_nps3_all_d
group by period_id, week_d, mon_d, qua_d, year_d, series, model, used_du, user_type, rom_type
union all
select CONCAT(date_sub(collect_set(period_id)[0], pmod(datediff(collect_set(period_id)[0], '1900-01-08'), 7)), '至', date_sub(collect_set(period_id)[0], pmod(datediff(collect_set(period_id)[0], '1900-01-08'), 7) - 6)) as period_id
	, week_d, mon_d, qua_d, year_d, series
	, model, used_du, user_type, rom_type
	, sum(out_num) as out_num, sum(mid_num) as mid_num
	, sum(in_num) as in_num
from oppo_daas_dev_dw.dwd_qty_nps3_all_d
group by week_d, mon_d, qua_d, year_d, series, model, used_du, user_type, rom_type
union all
select CONCAT(substr(collect_set(period_id)[0], 1, 7), '-01', '至', last_day(collect_set(period_id)[0])) as period_id
	, concat_ws('&',sort_array(collect_set(week_d))) as week_d, mon_d
	, qua_d, year_d, series, model, used_du
	, user_type, rom_type
 , sum(out_num) as out_num, sum(mid_num) as mid_num
	, sum(in_num) as in_num
from oppo_daas_dev_dw.dwd_qty_nps3_all_d
group by mon_d, qua_d, year_d, series, model, used_du, user_type, rom_type
union all
select qua_d as period_id, concat_ws('&',sort_array(collect_set(week_d))) as week_d
	, concat_ws('&',sort_array(collect_set(mon_d))) as mon_d, qua_d
	, year_d, series, model, used_du, user_type
	, rom_type
   , sum(out_num) as out_num, sum(mid_num) as mid_num
	, sum(in_num) as in_num
from oppo_daas_dev_dw.dwd_qty_nps3_all_d
group by qua_d, year_d, series, model, used_du, user_type, rom_type
union all
select CONCAT(substr(collect_set(period_id)[0], 1, 4), '-01-01', '至', substr(collect_set(period_id)[0], 1, 4), '-12-31') as period_id
	, CONCAT(sort_array(collect_set(week_d))[0], '至最后') as week_d
	, CONCAT(sort_array(collect_set(mon_d))[0], '至12') as mon_d
	, concat_ws('&',sort_array(collect_set(qua_d))) as qua_d, year_d
	, series, model, used_du, user_type, rom_type
	, sum(out_num) as out_num, sum(mid_num) as mid_num
	, sum(in_num) as in_num
from oppo_daas_dev_dw.dwd_qty_nps3_all_d
group by year_d, series, model, used_du, user_type, rom_type

)t1
```
