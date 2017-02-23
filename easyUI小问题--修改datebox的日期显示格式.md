#修改datebox的日期显示格式

一定要注意，要同时初始化formatter和parser两个方法。


    $.fn.datebox.defaults.formatter = function(date){
            var y = date.getFullYear();
            var m = date.getMonth()+1;
            var d = date.getDate();
            return y+'-'+m+'-'+d;
        }
    $.fn.datebox.defaults.parser = function(s){
            var t = Date.parse(s);
            if (!isNaN(t)){
                return new Date(t);
            } else {
                return new Date();
            }
        }
