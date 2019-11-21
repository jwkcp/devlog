---
layout: post
title: 리액트(React)에서 캔버스(Canvas) 다루는 방법
tags: react
comments: true
---

## 목표

리액트에서 캔버스 엘리먼트를 활용해 뭘 해보고 싶은데 어떻게 캔버스를 참조해야 하는지 모르겠어요. html과 jquery를 이용할 때는 `<canvas id='mycanvas' />` 처럼 해준 뒤 `document.getElementById('mycanvas')`로 캔버스의 참조를 가져올 수 있었거든요.  

---

## 방법

리액트는 가상DOM을 이용하기 때문에 canvas와 같은 실제 엘리먼트를 참조하기 위해서는 `React.createRef()`를 통한 ref를 이용해야 합니다. 생성자에서 참조를 만들고 `componentDidMount()`에서 컨택스트를 가져오면 됩니다. 아래 코드를 보시죠.  

{% highlight javascript %}
constructor(props) {
    super(props)
    this.canvasRef = React.createRef();
    this.ctx = null;
}

componentDidMount() {
    this.ctx = this.canvasRef.current.getContext("2d)
}

render() {
    return (
        <canvas ref={this.canvasRef} />
    )
}
{% endhighlight %}
