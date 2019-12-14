## es6

set使用

    let set = new Set();//创建
    let data = 111;
    set.add(data);//添加元素
    let len = set.size;//获取元素长度
    set.delete(data);//删除元素
    let key = set[data];//获取元素


数组去重
    
    //采用Set集合的特性
    function arr(arr){
        return [...new Set(arr)];
    }

    let arr = [1,2,3,4,4,4,5],
        newArr = arr(arr);
    
    console.log(newArr);//[1,2,3,4,5]
