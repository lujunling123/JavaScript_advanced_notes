### 数据的双向绑定 ### 
底层通过defineProperty函数来实现的，get读取之前的旧数据，set时如果发现原数据没有改变直接return 原始值，否则就修改为新数据

beforeCreate

created

beforeMount

mounted

beforeUpdate

updated

beforeDestroy

destroyed
