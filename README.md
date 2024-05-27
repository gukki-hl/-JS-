## JS原生模拟一个购物车

1. *一件商品的数据逻辑*`UIGool`

   - 添加**`choose`**(已选)属性
   - 获取总价:**`getTotalPrice`**
   - *是否选中此商品*:**`isChoose`**
   - *选择的数量+1*:**`increase`**
   - *选择的数量-1*:**`decrease`**

2. 整个购物车商品数据逻辑 `UIData`

   - 设置起送费 **`deliveryThreshold`** = 30
   - 设置配送费 **`deliveryPrice`**  = 5
   - 总价 **`getTotalPrice`**
   - 增加某件商品的选中数量**`increase(index)`**
   - 减少某件商品的选中数量**`decrease(index)`**
   - 得到购物车选中的商品数量**`getTotalChooseNumber`**
   - 查看购物车有没有东西**`hasGoodsInCar`**
   - 是否超过配送标准**`ifCrossDeliveryThreshold`**
   - 是否选中**`isChoose(index)`**

3. 整个界面 `UI`

   - 根据商品数据创建商品列表的元素(渲染html)   ------先拿到数据

   - 设置increase函数，decrease函数，并且调用`updateGoodsItem`,`updateFooter`,`jump`等函数

   - 当数据发生变化时调用更新某个商品元素的显示状态方法(active控制加号和减号)`this.updateGoodsItem(index)`

   - 当数据发送变化时调用注脚里的起送费active`this.updateFooter()`

   - 用户点击increase时抛物线`this.jump(index)`

   - 页脚状态`updateFooter`

     1. 得到总价数据`total`(保留两位小数)

     2. 设置配送费

     3. 是否可以起送

     4. 更新还差多少钱可以起送 四舍五入`Math.round(dis)`

     5. 设置购物车样式状态(active)

     6. 设置购物车中的数量(badge)

     7. 设置购物车动画(carAnimate)

     8. 设置抛物线(jump)

     9. 当页面加载时立即更新

        ​     this.createHTML()

        ​     this.updateFooter()

        ​     this.listenEvent()

4. 最后设置用户点击事件

项目源码

```
  //用js模拟一个购物车常用的一些逻辑 一件商品的数据逻辑
     class Gool{
        constructor(g){
            this.data = g
            this.choose = 0
        }
        //获取总价
        getTotalPrice(){
            return this.data.price * this.choose
        }

        //是否选中了此商品
        isChoose(){
            return this.choose >  0
        }
        
        //选择的数量+1
        increase(){
            this.choose++
        }

        decrease(){
            if(this.choose === 0){
                return
            }
            this.choose--
        }
    }

    //整个购物车商品数据逻辑
    class UIData{
        constructor(){
        const uiGoods = []
        for (var i = 0; i < goods.length; i++) {
            const uig = new Gool(goods[i]);
            uiGoods.push(uig);
          }
            this.uiGoods = uiGoods
            //设置起送费
            this.deliveryThreshold = 30
            //设置配送费
            this.deliveryPrice = 5
        }
        //总价
        getTotalPrice(){
            let sum = 0
            for(let i = 0;i < this.uiGoods.length;i++){
                const g = this.uiGoods[i]
                sum += g.getTotalPrice()
            }
            return sum
        }

        //增加某件商品的选中数量 
        increase(index){
            this.uiGoods[index].increase()
            console.log("要增加的商品索引值：", index);
        }
         //减少某件商品的选中数量
         decrease(index){
            this.uiGoods[index].decrease()
        }
        //得到购物车选中的商品的数量
        getTotalChooseNumber(){
            let number = 0
            for(let i = 0;i < this.uiGoods.length;i++){
                number += this.uiGoods[i].choose
            }
            return number
        }
   
        //查看购物车有没有东西
        hasGoodsInCar(){
            return this.getTotalChooseNumber() > 0
        }
   
        //是否超过配送标准
        ifCrossDeliveryThreshold(){
            return this.getTotalPrice() >= this.deliveryThreshold
        }

        //是否选中
        isChoose(index){
            return this.uiGoods[index].isChoose()
        }
    }

    //整个界面
    class UI{
        constructor(){
         this.uiData = new UIData()
         //收纳癖
         this.doms = {
            goodsContainer: document.querySelector('.goods-list'),
            // 获取注脚
            deliveryPrice: document.querySelector('.footer-car-tip'),
            footerPay:document.querySelector('.footer-pay'),
            footerPayInnerSpan:document.querySelector('.footer-pay span'),
            totalPrice:document.querySelector('.footer-car-total'),
            car:document.querySelector('.footer-car'),
            badge:document.querySelector('.footer-car-badge')
         }
             //得到购物车座标
             let carRect  =   this.doms.car.getBoundingClientRect()
             let jumpTarget = {
                 x: carRect.left + carRect.width /2,
                 y: carRect.top + carRect.height /5
             }
             this.jumpTarget = jumpTarget
             
         //当页面加载时立即更新
         this.createHTML()
         this.updateFooter()
         this.listenEvent()
        }
        
        // //监听各种事件
        listenEvent(){
            this.doms.car.addEventListener('animationend',function(){
                this.classList.remove('animate')
            })
        }

        //根据商品数据创建商品列表的元素
        createHTML(){
            // 两种方法：
            // 1.es6模板字符串拼接（执行效率低，开发效率高）
            // 2。动态生成（执行效率高，开发效率低）
            let html = ''
            for(let i = 0;i <this.uiData.uiGoods.length;i++){
               const g = this.uiData.uiGoods[i]
               console.log(g);
            html += `
            <div class="goods-item">
          <img src= ${g.data.pic} alt="" class="goods-pic" />
          <div class="goods-info">
            <h2 class="goods-title">${g.data.title}</h2>
            <p class="goods-desc">${g.data.desc}</p>
            <p class="goods-sell">
              <span> ${g.data.sellNumber}</span>
              <span> ${g.data.favorRate}</span>
            </p>
            <div class="goods-confirm">
              <p class="goods-price">
                <span class="goods-price-unit">￥</span>
                <span> ${g.data.price}</span>
              </p>
              <div class="goods-btns">
                <i index="${i}" class="iconfont i-jianhao"></i>
                <span>0</span>
                <i index= "${i}" class="iconfont i-jiajianzujianjiahao"></i>
              </div>
            </div>
          </div>
        </div>
            `
            }
            this.doms.goodsContainer.innerHTML = html
            // console.log(html);
        }

        increase(index){
            this.uiData.increase(index)
            // 当数据发生变化时调用更新某个商品元素的显示状态方法
            this.updateGoodsItem(index)
            //当数据发送变化时调用注脚里的起送费class
            this.updateFooter()
            //将抛物线效果给increase
            this.jump(index)
        }

        decrease(index){
            this.uiData.decrease(index)
            this.updateGoodsItem(index)
            this.updateFooter(index)
        }

        //更新某个商品元素的显示状态方法加号和减号
        updateGoodsItem(index){
            let goodsDom = this.doms.goodsContainer.children[index]
            if(this.uiData.isChoose(index)){
                goodsDom.classList.add('active')
            }else{
                goodsDom.classList.remove('active')
            }
            //显示当前选中文本
            const span = goodsDom.querySelector('.goods-btns span')
            span.textContent = this.uiData.uiGoods[index].choose
        }

        //更新页脚
        updateFooter(){
            //得到总价数据
            let total = this.uiData.getTotalPrice()
            // 设置配送费
            this.doms.deliveryPrice.textContent = `配送费￥${this.uiData.deliveryPrice}`
            //是否可以起送
            if(this.uiData.ifCrossDeliveryThreshold()){
                //设置起送高亮效果
                this.doms.footerPay.classList.add('active')
            }else{
                this.doms.footerPay.classList.remove('active')
                // 更新还差多少钱
               let dis =  this.uiData.deliveryThreshold - total
               dis = Math.round(dis)//四舍五入
               this.doms.footerPayInnerSpan.textContent= `还差￥${dis}元起送`

            }

            //设置总价
            this.doms.totalPrice.textContent = total.toFixed(2)//保留两位小数
            //设置购物车样式状态
            if(this.uiData.hasGoodsInCar()){
                this.doms.car.classList.add('active')
            }else{
                this.doms.car.classList.remove('active')
            }
            //设置购物车中的数量
            this.doms.badge.textContent = this.uiData.getTotalChooseNumber()
        }

        //购物车动画
        carAnimate(){
            this.doms.car.classList.add('animate')
        }

        //设置抛物线
        jump(index){
        //找到对应商品的加号
       const btnAdd = this.doms.goodsContainer.children[index].querySelector('.i-jiajianzujianjiahao')
       //拿到商品加号的坐标
       let rect = btnAdd.getBoundingClientRect()
       //起始坐标
       let start = { x:rect.left, y:rect.top }
    //创建一个div容器用于抛物的载体
    const div = document.createElement('div')
    div.className = 'add-to-car'
    const i = document.createElement('i')
    i.className = 'iconfont i-jiajianzujianjiahao'
    //设置初始坐标  div控制横向 i控制纵向
    div.style.transform = `translateX(${start.x}px)`
    i.style.transform = `translateY(${start.y}px)`
    div.appendChild(i)
    document.body.appendChild(div)
    // 强行渲染 
    div.clientWidth

    //设置结束位置
    div.style.transform = `translateX(${this.jumpTarget.x}px)`
    i.style.transform = `translateY(${this.jumpTarget.y}px)`

    //设置跳跃后样式
    div.addEventListener('transitionend',()=>{
        div.remove()
        this.carAnimate()
    })

    }
    }


    const gs = new Gool(goods[0])
    const uidata = new UIData()
    const ui = new UI()

    //设置点击事件
    ui.doms.goodsContainer.addEventListener('click',(e)=>{
        if(e.target.classList.contains('i-jiajianzujianjiahao')){
          let index = +e.target.getAttribute('index')
          ui.increase(index)
        }else if(e.target.classList.contains('i-jianhao')){
            let index = +e.target.getAttribute('index')
            ui.decrease(index)
        }
    })
    // console.log(ui);
```

