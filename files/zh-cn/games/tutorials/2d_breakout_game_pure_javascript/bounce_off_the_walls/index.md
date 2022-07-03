---
title: 反弹的墙壁
slug: Games/Tutorials/2D_Breakout_game_pure_JavaScript/Bounce_off_the_walls
translation_of: Games/Tutorials/2D_Breakout_game_pure_JavaScript/Bounce_off_the_walls
---
<div>{{GamesSidebar}}</div>

<p>{{PreviousNext("Games/Workflows/2D_Breakout_game_pure_JavaScript/Move_the_ball", "Games/Workflows/2D_Breakout_game_pure_JavaScript/Paddle_and_keyboard_controls")}}</p>

<div>
<p>本篇是 <a href="/en-US/docs/Games/Workflows/Breakout_game_from_scratch">Gamedev Canvas tutorial</a> 10 节教程中的第三节。如果你完成了本篇教程之后，你可以从 <a href="https://github.com/end3r/Gamedev-Canvas-workshop/blob/gh-pages/lesson03.html">Gamedev-Canvas-workshop/lesson3.html</a> 看到源码。</p>
</div>

<p>看到我们的球动起来很惊讶吧，但是它很快就从屏幕上消失了，当然我们是可以控制它的。我们会实现一些非常简单的碰撞检测 (详细后面解释),使球在画布的四周反弹回来。</p>

<h2 id="简单的碰撞">简单的碰撞</h2>

<p>我们将检查球体是否与边缘接触，如果有接触我们将相应的改变它的运动方向。</p>

<p>为了运算方便我们定义一个名为 ballRadius 的变量，来存储球的半径。向代码中添加一下内容：</p>

<pre class="brush: js">var ballRadius = 10;</pre>

<p>现在更新绘制球的 drawBall() 函数：</p>

<pre class="brush: js">ctx.arc(x, y, ballRadius, 0, Math.PI*2);</pre>

<h3 id="从顶部和底部弹起">从顶部和底部弹起</h3>

<p>有四面墙壁可以让它反弹回来，我们先来看上面的那面墙。我们需要判断球运动的每一帧，球体是否与画布的顶部边缘接触。如果有接触，我们将会改变球体的运动方向，使它向相反的方向移动，并保证它在画布的可见范围之内。记住坐标系统的左上角，让我们开始并加以下代码：</p>

<pre class="brush: js">if(y + dy &lt; 0) {
    dy = -dy;
}</pre>

<p>如果球的纵坐标（y 轴）值小于零，我们将在球体原有的运动方向上逆转。如果球体向上移动的速度是 2 像素/帧，现在就是向上移动速度是 -2 像素。这相当于此时向下移动的速度是 2 像素/帧。</p>

<p>上面的代码将处理球与画布顶部边缘的反射，现在让我们思考一下底部边缘如何处理：</p>

<pre class="brush: js">if(y + dy &gt; canvas.height) {
    dy = -dy;
}</pre>

<p>如果球的 y 位置大于<code>canvas</code>的高度（记住，我们从左上角计算 y 值，所以顶部边缘从 0 开始，底部边缘在 480 像素），然后通过像以前那样反转 y 轴运动而离开底部边缘。</p>

<p>我们可以将这两句冗长的代码合二为一：</p>

<pre class="brush: js">if(y + dy &gt; canvas.height || y + dy &lt; 0) {
    dy = -dy;
}</pre>

<p>如果其中一个判断为 <code>true</code>, 则反转球的运动。</p>

<h3 id="从左边和右边反弹">从左边和右边反弹</h3>

<p>我们有顶部和底部的边缘，所以我们来考虑一下左边和右边的边缘。实际上非常相似，你所要做的就是颠倒<code>x</code>而不是<code>y</code>:</p>

<pre class="brush: js">if(x + dx &gt; canvas.width || x + dx &lt; 0) {
    dx = -dx;
}

if(y + dy &gt; canvas.height || y + dy &lt; 0) {
    dy = -dy;
}</pre>

<p>你应该把上面的代码块插入到 draw（）函数中，就在大括号之前。</p>

<h3 id="球部分消失在墙上！">球部分消失在墙上！</h3>

<p>测试你的代码，你会看到我们的球碰到任一边缘都会反弹！然而，我们还发现了一个问题，当球碰撞到边缘，反弹之前：</p>

<p><img src="ball-in-wall.png"></p>

<p>这是因为我们正在计算墙和球的中心碰撞点，而我们应该围绕它的周长来做。如果碰到墙壁，球应该会弹起来，而不是陷入墙壁一半时，所以让我们来调整一下我们的判断条件。更新你之前添加的代码：</p>

<pre class="brush: js">if(x + dx &gt; canvas.width-ballRadius || x + dx &lt; ballRadius) {
    dx = -dx;
}
if(y + dy &gt; canvas.height-ballRadius || y + dy &lt; ballRadius) {
    dy = -dy;
}</pre>

<p>当球的中心到墙的边缘之间的距离与球的半径完全相同时，它将改变运动的方向。</p>

<h2 id="比较你的代码">比较你的代码</h2>

<p>让我们再次检查这个部分的代码与你之间有何差异：</p>

<p>{{JSFiddleEmbed("https://jsfiddle.net/end3r/redj37dc/","","370")}}</p>

<div class="note">
<p><strong>备注：</strong> 尝试修改你的代码，在每次碰到墙壁时都要把球的颜色改成随机的颜色。</p>
</div>

<h2 id="下一步">下一步</h2>

<p>现在我们已经到了我们的球正在移动和留在游戏板上的阶段。在第四章中，我们将看看如何实现一个可控制的 paddle - 参见<a href="/en-US/docs/Games/Workflows/Breakout_game_from_scratch/Paddle_and_keyboard_controls">paddle 和键盘控制</a>。</p>

<p>{{PreviousNext("Games/Workflows/2D_Breakout_game_pure_JavaScript/Move_the_ball", "Games/Workflows/2D_Breakout_game_pure_JavaScript/Paddle_and_keyboard_controls")}}</p>