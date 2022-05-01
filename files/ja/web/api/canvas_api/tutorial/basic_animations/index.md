---
title: Basic animations
slug: Web/API/Canvas_API/Tutorial/Basic_animations
tags:
  - Canvas
  - Graphics
  - HTML
  - HTML5
  - Intermediate
  - Tutorial
translation_of: Web/API/Canvas_API/Tutorial/Basic_animations
original_slug: Web/Guide/HTML/Canvas_tutorial/Basic_animations
---
<div>{{CanvasSidebar}} {{PreviousNext("Web/API/Canvas_API/Tutorial/Compositing", "Web/API/Canvas_API/Tutorial/Advanced_animations")}}</div>

<div class="summary">
<p>私たちが {{HTMLElement("canvas")}} 要素の操作に JavaScript を使うのは、とても簡単にインタラクティブなアニメーションを作成できるからです！本章では、いくつかの基本的なアニメーションで、その概要をつかんでいきます。</p>
</div>

<p>おそらく最大の制約は、キャンバスに図形を一度描画すると、その状態が維持されることです。アニメーションさせる場合にも、移動する部分と以前に描いた部分をすべて再描画する必要があります。複雑なフレームの再描画には時間がかかり、パフォーマンスは、実行しているコンピューターの速度に大きく依存します。</p>

<h2 id="Basic_animation_steps" name="Basic_animation_steps">基本的なアニメーションの手順</h2>

<p>フレームを描画させる手順は、このようになります。</p>

<ol>
 <li><strong>キャンバスをクリアする</strong><br>
  描画する図形がキャンバス全体 (たとえば、背景画像) に収まらない限り、以前に描画した図形をすべてクリアする必要があります。それを行う最も簡単な方法は、{{domxref("CanvasRenderingContext2D.clearRect", "clearRect()")}} メソッドを使うことです。</li>
 <li><strong>キャンバスの状態を保存する</strong><br>
  キャンバスの状態に影響を与える設定(スタイル、変形など)を変更していて、フレームを描画するたびに元の状態を使用したい場合は、その状態を保存する必要があります。</li>
 <li><strong>アニメ―ションさせる図形を描画する</strong><br>
  実際に、フレームの描画を行います。</li>
 <li><strong>キャンバスの状態を復元する</strong><br>
  状態を保存した場合は、新しいフレームを描画する前に状態を復元します。</li>
</ol>

<h2 id="Controlling_an_animation" name="Controlling_an_animation">アニメーションの制御</h2>

<p>図形は、canvas のメソッドを直接使用するか、カスタム関数を呼び出すことによって描画されます。通常は、スクリプトの実行が終了したときにのみ、これらの結果がキャンバスに表示されます。たとえば、<code>for</code> ループ内からアニメーションを実行することはできません。</p>

<p>つまり、一定の期間ごとに描画関数を実行する方法が必要です。このようなアニメーションを制御するには、2 つの方法があります。</p>

<h3 id="Scheduled_updates" name="Scheduled_updates">スケジュールの更新</h3>

<p>まず、{{domxref("window.setInterval()")}}、{{domxref("window.setTimeout()")}} があります。それから、{{domxref("window.requestAnimationFrame()")}} 関数があります。これらは、特定の関数を一定時間で呼び出すために使用できます。</p>

<dl>
 <dt>{{domxref("WindowTimers.setInterval", "setInterval(function, delay)")}}</dt>
 <dd><code>function</code> で指定した関数を <code>delay</code> ミリ秒ごとに繰り返し実行します。</dd>
 <dt>{{domxref("WindowTimers.setTimeout", "setTimeout(function, delay)")}}</dt>
 <dd><code>function</code> で指定した関数を <code>delay</code> ミリ秒後に実行します。</dd>
 <dt>{{domxref("Window.requestAnimationFrame()", "requestAnimationFrame(callback)")}}</dt>
 <dd>アニメーションを実行することをブラウザーに通知し、次の再描画の前にアニメーションを更新するため、ブラウザーが指定の関数を呼び出すように要求します。</dd>
</dl>

<p>ユーザーの操作が必要ない場合は、提供されたコードを繰り返し実行する <code>setInterval()</code> 関数を使用できます。ゲームを作成したい場合、キーボードまたはマウスのイベントを使用してアニメーションを制御するため <code>setTimeout()</code> を使用できます。{{domxref( "EventListener")}}を設定することで、ユーザーの操作を取得し、アニメーション関数を実行します。</p>

<div class="note">
<p>以下の例では、{{domxref("window.requestAnimationFrame()")}} メソッドを使用してアニメーションを制御します。<code>requestAnimationFrame</code> メソッドは、フレームを描画する準備ができた時にシステムがアニメーションフレームを呼び出すことで、よりスムーズで効率的な方法でアニメーションを提供します。通常、コールバック回数は 1 秒あたり 60 回となり、バックグラウンドタブで実行している場合は、レートが低くなることがあります。特にゲームのアニメーションループの詳細については、<a href="/ja/docs/Games">ゲーム開発</a>の<a href="/ja/docs/Games/Anatomy">ビデオゲームの解剖学</a>を参照してください。</p>
</div>

<h2 id="An_animated_solar_system" name="An_animated_solar_system">アニメーションする太陽系</h2>

<p>この例は、太陽系の小さなモデルをアニメーションさせます。</p>

<pre class="brush: js notranslate">var sun = new Image();
var moon = new Image();
var earth = new Image();
function init(){
  sun.src = 'https://mdn.mozillademos.org/files/1456/Canvas_sun.png';
  moon.src = 'https://mdn.mozillademos.org/files/1443/Canvas_moon.png';
  earth.src = 'https://mdn.mozillademos.org/files/1429/Canvas_earth.png';
  window.requestAnimationFrame(draw);
}

function draw() {
  var ctx = document.getElementById('canvas').getContext('2d');

  ctx.globalCompositeOperation = 'destination-over';
  ctx.clearRect(0,0,300,300); // clear canvas

  ctx.fillStyle = 'rgba(0,0,0,0.4)';
  ctx.strokeStyle = 'rgba(0,153,255,0.4)';
  ctx.save();
  ctx.translate(150,150);

  // Earth
  var time = new Date();
  ctx.rotate( ((2*Math.PI)/60)*time.getSeconds() + ((2*Math.PI)/60000)*time.getMilliseconds() );
  ctx.translate(105,0);
  ctx.fillRect(0,-12,50,24); // Shadow
  ctx.drawImage(earth,-12,-12);

  // Moon
  ctx.save();
  ctx.rotate( ((2*Math.PI)/6)*time.getSeconds() + ((2*Math.PI)/6000)*time.getMilliseconds() );
  ctx.translate(0,28.5);
  ctx.drawImage(moon,-3.5,-3.5);
  ctx.restore();

  ctx.restore();

  ctx.beginPath();
  ctx.arc(150,150,105,0,Math.PI*2,false); // Earth orbit
  ctx.stroke();

  ctx.drawImage(sun,0,0,300,300);

  window.requestAnimationFrame(draw);
}

init();
</pre>

<div class="hidden">
<pre class="brush: html notranslate">&lt;canvas id="canvas" width="300" height="300"&gt;&lt;/canvas&gt;</pre>
</div>

<p>{{EmbedLiveSample("An_animated_solar_system", "310", "310", "https://mdn.mozillademos.org/files/202/Canvas_animation1.png")}}</p>

<h2 id="An_animated_clock" name="An_animated_clock">アニメ―ションする時計</h2>

<p>この例は、アニメーションする時計で現在時間を表示します。</p>

<pre class="brush: js notranslate">function clock(){
  var now = new Date();
  var ctx = document.getElementById('canvas').getContext('2d');
  ctx.save();
  ctx.clearRect(0,0,150,150);
  ctx.translate(75,75);
  ctx.scale(0.4,0.4);
  ctx.rotate(-Math.PI/2);
  ctx.strokeStyle = "black";
  ctx.fillStyle = "white";
  ctx.lineWidth = 8;
  ctx.lineCap = "round";

  // Hour marks
  ctx.save();
  for (var i=0;i&lt;12;i++){
    ctx.beginPath();
    ctx.rotate(Math.PI/6);
    ctx.moveTo(100,0);
    ctx.lineTo(120,0);
    ctx.stroke();
  }
  ctx.restore();

  // Minute marks
  ctx.save();
  ctx.lineWidth = 5;
  for (i=0;i&lt;60;i++){
    if (i%5!=0) {
      ctx.beginPath();
      ctx.moveTo(117,0);
      ctx.lineTo(120,0);
      ctx.stroke();
    }
    ctx.rotate(Math.PI/30);
  }
  ctx.restore();

  var sec = now.getSeconds();
  var min = now.getMinutes();
  var hr  = now.getHours();
  hr = hr&gt;=12 ? hr-12 : hr;

  ctx.fillStyle = "black";

  // write Hours
  ctx.save();
  ctx.rotate( hr*(Math.PI/6) + (Math.PI/360)*min + (Math.PI/21600)*sec )
  ctx.lineWidth = 14;
  ctx.beginPath();
  ctx.moveTo(-20,0);
  ctx.lineTo(80,0);
  ctx.stroke();
  ctx.restore();

  // write Minutes
  ctx.save();
  ctx.rotate( (Math.PI/30)*min + (Math.PI/1800)*sec )
  ctx.lineWidth = 10;
  ctx.beginPath();
  ctx.moveTo(-28,0);
  ctx.lineTo(112,0);
  ctx.stroke();
  ctx.restore();

  // Write seconds
  ctx.save();
  ctx.rotate(sec * Math.PI/30);
  ctx.strokeStyle = "#D40000";
  ctx.fillStyle = "#D40000";
  ctx.lineWidth = 6;
  ctx.beginPath();
  ctx.moveTo(-30,0);
  ctx.lineTo(83,0);
  ctx.stroke();
  ctx.beginPath();
  ctx.arc(0,0,10,0,Math.PI*2,true);
  ctx.fill();
  ctx.beginPath();
  ctx.arc(95,0,10,0,Math.PI*2,true);
  ctx.stroke();
  ctx.fillStyle = "rgba(0,0,0,0)";
  ctx.arc(0,0,3,0,Math.PI*2,true);
  ctx.fill();
  ctx.restore();

  ctx.beginPath();
  ctx.lineWidth = 14;
  ctx.strokeStyle = '#325FA2';
  ctx.arc(0,0,142,0,Math.PI*2,true);
  ctx.stroke();

  ctx.restore();

  window.requestAnimationFrame(clock);
}

window.requestAnimationFrame(clock);</pre>

<div class="hidden">
<pre class="brush: html notranslate">&lt;canvas id="canvas" width="150" height="150"&gt;&lt;/canvas&gt;</pre>
</div>

<p>{{EmbedLiveSample("An_animated_clock", "180", "180", "https://mdn.mozillademos.org/files/203/Canvas_animation2.png")}}</p>

<h2 id="A_looping_panorama" name="A_looping_panorama">ループする風景</h2>

<p>この例は、左から右へ風景写真をスクロールさせます。Wikipedia から<a href="https://commons.wikimedia.org/wiki/File:Capitan_Meadows,_Yosemite_National_Park.jpg?uselang=ja">ヨセミテ国立公園の画像</a>を使いましたが、キャンバスよりも大きな任意の画像を使用できます。</p>

<pre class="brush: js notranslate">var img = new Image();

// User Variables - customize these to change the image being scrolled, its
// direction, and the speed.

img.src = 'https://mdn.mozillademos.org/files/4553/Capitan_Meadows,_Yosemite_National_Park.jpg';
var CanvasXSize = 800;
var CanvasYSize = 200;
var speed = 30; // lower is faster
var scale = 1.05;
var y = -4.5; // vertical offset

// Main program

var dx = 0.75;
var imgW;
var imgH;
var x = 0;
var clearX;
var clearY;
var ctx;

img.onload = function() {
    imgW = img.width * scale;
    imgH = img.height * scale;

    if (imgW &gt; CanvasXSize) {
        // image larger than canvas
        x = CanvasXSize - imgW;
    }
    if (imgW &gt; CanvasXSize) {
        // image width larger than canvas
        clearX = imgW;
    } else {
        clearX = CanvasXSize;
    }
    if (imgH &gt; CanvasYSize) {
        // image height larger than canvas
        clearY = imgH;
    } else {
        clearY = CanvasYSize;
    }

    // get canvas context
    ctx = document.getElementById('canvas').getContext('2d');

    // set refresh rate
    return setInterval(draw, speed);
}

function draw() {
    ctx.clearRect(0, 0, clearX, clearY); // clear the canvas

    // if image is &lt;= Canvas Size
    if (imgW &lt;= CanvasXSize) {
        // reset, start from beginning
        if (x &gt; CanvasXSize) {
            x = -imgW + x;
        }
        // draw additional image1
        if (x &gt; 0) {
            ctx.drawImage(img, -imgW + x, y, imgW, imgH);
        }
        // draw additional image2
        if (x - imgW &gt; 0) {
            ctx.drawImage(img, -imgW * 2 + x, y, imgW, imgH);
        }
    }

    // image is &gt; Canvas Size
    else {
        // reset, start from beginning
        if (x &gt; (CanvasXSize)) {
            x = CanvasXSize - imgW;
        }
        // draw aditional image
        if (x &gt; (CanvasXSize-imgW)) {
            ctx.drawImage(img, x - imgW + 1, y, imgW, imgH);
        }
    }
    // draw image
    ctx.drawImage(img, x, y,imgW, imgH);
    // amount to move
    x += dx;
}
</pre>

<p>以下は、画像をスクロールする {{HTMLElement("canvas")}} です。ここで指定する幅と高さは、JavaScript コードの <code>CanvasXZSize</code> および <code>CanvasYSize</code> 変数の値と一致する必要があることに注意してください。</p>

<pre class="brush: html notranslate">&lt;canvas id="canvas" width="800" height="200"&gt;&lt;/canvas&gt;</pre>

<p>{{EmbedLiveSample("A_looping_panorama", "830", "230")}}</p>

<h2 id="Mouse_Following_Animation" name="Mouse_Following_Animation">マウス追跡アニメーション</h2>

<pre class="brush: html notranslate">&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;
    &lt;head&gt;
        &lt;meta charset="UTF-8"&gt;
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
        &lt;meta http-equiv="X-UA-Compatible" content="ie=edge"&gt;
        &lt;title&gt;Document&lt;/title&gt;
        &lt;script&gt;
            var cn;
            //= document.getElementById('cw');
            var c;
            var u = 10;
            const m = {
                x: innerWidth / 2,
                y: innerHeight / 2
            };
            window.onmousemove = function(e) {
                m.x = e.clientX;
                m.y = e.clientY;

            }
            function gc() {
                var s = "0123456789ABCDEF";
                var c = "#";
                for (var i = 0; i &lt; 6; i++) {
                    c += s[Math.ceil(Math.random() * 15)]
                }
                return c
            }
            var a = [];
            window.onload = function myfunction() {
                cn = document.getElementById('cw');
                c = cn.getContext('2d');

                for (var i = 0; i &lt; 10; i++) {
                    var r = 30;
                    var x = Math.random() * (innerWidth - 2 * r) + r;
                    var y = Math.random() * (innerHeight - 2 * r) + r;
                    var t = new ob(innerWidth / 2,innerHeight / 2,5,"red",Math.random() * 200 + 20,2);
                    a.push(t);
                }
                //cn.style.backgroundColor = "#700bc8";

                c.lineWidth = "2";
                c.globalAlpha = 0.5;
                resize();
                anim()
            }
            window.onresize = function() {

                resize();

            }
            function resize() {
                cn.height = innerHeight;
                cn.width = innerWidth;
                for (var i = 0; i &lt; 101; i++) {
                    var r = 30;
                    var x = Math.random() * (innerWidth - 2 * r) + r;
                    var y = Math.random() * (innerHeight - 2 * r) + r;
                    a[i] = new ob(innerWidth / 2,innerHeight / 2,4,gc(),Math.random() * 200 + 20,0.02);

                }
                //  a[0] = new ob(innerWidth / 2, innerHeight / 2, 40, "red", 0.05, 0.05);
                //a[0].dr();
            }
            function ob(x, y, r, cc, o, s) {
                this.x = x;
                this.y = y;
                this.r = r;
                this.cc = cc;
                this.theta = Math.random() * Math.PI * 2;
                this.s = s;
                this.o = o;
                this.t = Math.random() * 150;

                this.o = o;
                this.dr = function() {
                    const ls = {
                        x: this.x,
                        y: this.y
                    };
                    this.theta += this.s;
                    this.x = m.x + Math.cos(this.theta) * this.t;
                    this.y = m.y + Math.sin(this.theta) * this.t;
                    c.beginPath();
                    c.lineWidth = this.r;
                    c.strokeStyle = this.cc;
                    c.moveTo(ls.x, ls.y);
                    c.lineTo(this.x, this.y);
                    c.stroke();
                    c.closePath();

                }
            }
            function anim() {
                requestAnimationFrame(anim);
                c.fillStyle = "rgba(0,0,0,0.05)";
                c.fillRect(0, 0, cn.width, cn.height);
                a.forEach(function(e, i) {
                    e.dr();
                });

            }
        &lt;/script&gt;
        &lt;style&gt;
            #cw {
                position: fixed;
                z-index: -1;
            }

            body {
                margin: 0;
                padding: 0;
                background-color: rgba(0,0,0,0.05);
            }
        &lt;/style&gt;
    &lt;/head&gt;
    &lt;body&gt;
        &lt;canvas id="cw"&gt;&lt;/canvas&gt;
    &lt;/body&gt;
&lt;/html&gt;
</pre>

<h5 id="OutPut" name="OutPut">表示例</h5>

<table class="standard-table">
 <tbody>
  <tr>
   <td>
    <p><a href="https://kunalverma94.github.io/gallery/gags/beyblade.html"><img alt="beyblade" src="https://kunalverma94.github.io/gallery/beyblade.jpg" style="height: 298px; width: 399px;"></a></p>
   </td>
  </tr>
 </tbody>
</table>

<h2 id="Snake_Game" name="Snake_Game">スネークゲーム</h2>

<pre class="brush: html notranslate">&lt;!DOCTYPE html&gt;
&lt;html lang="en"&gt;

&lt;head&gt;
    &lt;meta charset="UTF-8"&gt;
    &lt;meta name="viewport" content="width=device-width,initial-scale=1"&gt;
    &lt;meta http-equiv="X-UA-Compatible" content="ie=edge"&gt;
    &lt;title&gt;Nokia 1100:snake..Member berries&lt;/title&gt;
&lt;/head&gt;

&lt;body&gt;
    &lt;div class="keypress hide"&gt;
        &lt;div class="up" onclick="emit(38)"&gt;&amp;#8593;&lt;/div&gt;
        &lt;div class="right" onclick="emit(39)"&gt;&amp;#8594;&lt;/div&gt;
        &lt;div class="left" onclick="emit(37)"&gt;&amp;#8592;&lt;/div&gt;
        &lt;div class="down" onclick="emit(40)"&gt;&amp;#8595;&lt;/div&gt;
    &lt;/div&gt;
    &lt;div class="banner" id="selector"&gt;
        &lt;div&gt;
            Time :&lt;span id="time"&gt;0&lt;/span&gt;
        &lt;/div&gt;
        &lt;div&gt;LousyGames ©&lt;/div&gt;
        &lt;div&gt;
            Score :&lt;span id="score"&gt;0&lt;/span&gt;
        &lt;/div&gt;
        &lt;div class="touch off" onclick="touch(this)"&gt;touch&lt;/div&gt;
    &lt;/div&gt;
    &lt;canvas id="main"&gt;&lt;/canvas&gt;
&lt;/body&gt;
&lt;style&gt;
    body {
        margin: 0;
        overflow: hidden;
        background: #000
    }

    .banner {
        text-align: center;
        color: #fff;
        background: #3f51b5;
        line-height: 29px;
        position: fixed;
        left: 0;
        top: 0;
        right: 0;
        font-family: monospace;
        height: 30px;
        opacity: .4;
        display: flex;
        transition: .5s
    }

    .banner:hover {
        opacity: 1
    }

    div#selector&gt;div {
        flex-basis: 30%
    }

    @keyframes diss {
        from {
            opacity: 1
        }

        to {
            opacity: 0
        }
    }

    .keypress&gt;div {
        border: dashed 3px #fff;
        height: 48%;
        width: 48%;
        display: flex;
        align-content: center;
        justify-content: center;
        align-self: center;
        align-items: center;
        font-size: -webkit-xxx-large;
        font-weight: 900;
        color: #fff;
        transition: .5s;
        opacity: .1;
        border-radius: 7px
    }

    .keypress {
        position: fixed;
        width: 100vw;
        height: 100vh;
        top: 0;
        left: 0;
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;
        opacity: 1;
        user-select: none
    }

    .keypress&gt;div:hover {
        opacity: 1
    }

    .touch {
        background: #8bc34a
    }

    .off {
        background: #f44336
    }

    .hide {
        opacity: 0
    }
&lt;/style&gt;
&lt;/html&gt;</pre>

<p>Javascript</p>

<pre class="brush: js notranslate">function tmz() {
        var e = new Date(t),
            i = new Date,
            n = Math.abs(i.getMinutes() - e.getMinutes()),
            o = Math.abs(i.getSeconds() - e.getSeconds());
        return n + " : " + o
    }

    function coll(t, e) {
        return t.x &lt; e.x + e.w &amp;&amp; t.x + t.w &gt; e.x &amp;&amp; t.y &lt; e.y + e.h &amp;&amp; t.h + t.y &gt; e.y
    }

    function snake() {
        this.w = 15, this.h = 15, this.dx = 1, this.dy = 1, this.xf = 1, this.yf = 1, this.sn = [];
        for (var t = {
            x: w / 2,
            y: h / 2
        }, e = 0; e &lt; 5; e++) this.sn.push(Object.assign({}, t)), t.x += this.w;
        this.draw = function () {
            var t = d &amp;&amp; d.search("Arrow") &gt; -1,
                e = -1;
            if (t) {
                var i = {
                    ...this.sn[0]
                };
                if ("ArrowUp" == d &amp;&amp; (i.y -= this.h), "ArrowDown" == d &amp;&amp; (i.y += this.h), "ArrowLeft" == d &amp;&amp; (i.x -= this.w), "ArrowRight" == d &amp;&amp; (i.x += this.w), i.x &gt;= w ? i.x = 0 : i.x &lt; 0 &amp;&amp; (i.x = w - this.w), i.y &gt; h ? i.y = 0 : i.y &lt; 0 &amp;&amp; (i.y = h), e = fa.findIndex(t =&gt; coll({
                    ...this.sn[0],
                    h: this.h,
                    w: this.w
                }, t)), this.sn.unshift(i), -1 != e) return console.log(e), fa[e].renew(), void (document.getElementById("score").innerText = Number(document.getElementById("score").innerText) + 1);
                this.sn.pop(), console.log(6)
            }
            this.sn.forEach((t, e, i) =&gt; {
                if (0 == e || i.length - 1 == e) {
                    var n = c.createLinearGradient(t.x, t.y, t.x + this.w, t.y + this.h);
                    i.length - 1 == e ? (n.addColorStop(0, "black"), n.addColorStop(1, "#8BC34A")) : (n.addColorStop(0, "#8BC34A"), n.addColorStop(1, "white")), c.fillStyle = n
                } else c.fillStyle = "#8BC34A";
                c.fillRect(t.x, t.y, this.w, this.h), c.strokeStyle = "#E91E63", c.font = "30px serif", c.strokeStyle = "#9E9E9E", i.length - 1 != e &amp;&amp; 0 != e &amp;&amp; c.strokeRect(t.x, t.y, this.w, this.h), 0 == e &amp;&amp; (c.beginPath(), c.fillStyle = "#F44336", c.arc(t.x + 10, t.y + 2, 5, 360, 0), c.fill()), c.arc(t.x + 10, t.y + 2, 5, 360, 0), c.fill(), c.beginPath()
            })
        }
    }

    function gc() {
        for (var t = "0123456789ABCDEF", e = "#", i = 0; i &lt; 6; i++) e += t[Math.ceil(15 * Math.random())];
        return e
    }

    function food() {
        this.x = 0, this.y = 0, this.b = 10, this.w = this.b, this.h = this.b, this.color = gc(), this.renew = function () {
            this.x = Math.floor(Math.random() * (w - 200) + 10), this.y = Math.floor(Math.random() * (h - 200) + 30), this.color = gc()
        }, this.renew(), this.put = (() =&gt; {
            c.fillStyle = this.color, c.arc(this.x, this.y, this.b - 5, 0, 2 * Math.PI), c.fill(), c.beginPath(), c.arc(this.x, this.y, this.b - 5, 0, Math.PI), c.strokeStyle = "green", c.lineWidth = 10, c.stroke(), c.beginPath(), c.lineWidth = 1
        })
    }

    function init() {
        cc.height = h, cc.width = w, c.fillRect(0, 0, w, innerHeight);
        for (var t = 0; t &lt; 10; t++) fa.push(new food);
        s = new snake(w / 2, h / 2, 400, 4, 4), anima()
    }

    function anima() {
        c.fillStyle = "rgba(0,0,0,0.11)", c.fillRect(0, 0, cc.width, cc.height), fa.forEach(t =&gt; t.put()), s.draw(), document.getElementById("time").innerText = tmz(), setTimeout(() =&gt; {
            requestAnimationFrame(anima)
        }, fw)
    }

    function emit(t) {
        key.keydown(t)
    }

    function touch(t) {
        t.classList.toggle("off"), document.getElementsByClassName("keypress")[0].classList.toggle("hide")
    }
    var t = new Date + "",
        d = void 0,
        cc = document.getElementsByTagName("canvas")[0],
        c = cc.getContext("2d");
    key = {}, key.keydown = function (t) {
        var e = document.createEvent("KeyboardEvent");
        Object.defineProperty(e, "keyCode", {
            get: function () {
                return this.keyCodeVal
            }
        }), Object.defineProperty(e, "key", {
            get: function () {
                return 37 == this.keyCodeVal ? "ArrowLeft" : 38 == this.keyCodeVal ? "ArrowUp" : 39 == this.keyCodeVal ? "ArrowRight" : "ArrowDown"
            }
        }), Object.defineProperty(e, "which", {
            get: function () {
                return this.keyCodeVal
            }
        }), e.initKeyboardEvent ? e.initKeyboardEvent("keydown", !0, !0, document.defaultView, !1, !1, !1, !1, t, t) : e.initKeyEvent("keydown", !0, !0, document.defaultView, !1, !1, !1, !1, t, 0), e.keyCodeVal = t, e.keyCode !== t &amp;&amp; alert("keyCode mismatch " + e.keyCode + "(" + e.which + ")"), document.dispatchEvent(e)
    };
    var o, s, h = innerHeight,
        w = innerWidth,
        fw = 60,
        fa = [];
    window.onkeydown = function (t) {
        var e = t.key;
        (e.search("Arrow") &gt; -1 || "1" == e) &amp;&amp; (d = t.key), "i" != e &amp;&amp; "I" != e || (console.log("inc"), fw -= 10), "d" != e &amp;&amp; "D" != e || (console.log("dec"), fw += 10)
    }, init();
</pre>

<h5 id="Output_2" name="Output_2">表示例</h5>

<table class="standard-table">
 <tbody>
  <tr>
   <td>
    <h2 id="sect1"><a href="https://kunalverma94.github.io/pokemon/snake.html"><img alt="Snake game" src="https://kunalverma94.github.io/view/images/snake.jpg" style="height: 400px; width: 600px;"></a></h2>
   </td>
  </tr>
 </tbody>
</table>

<h2 id="Other_examples" name="Other_examples">その他のサンプル</h2>

<dl>
 <dt><a href="/ja/docs/Web/API/Canvas_API/A_basic_ray-caster" title="/ja/docs/Web/Guide/HTML/A_basic_ray-caster">A basic ray-caster</a></dt>
 <dd>キーボードを使ってアニメーションをどのように制御するか説明した良いサンプルです。</dd>
 <dt><a href="/ja/docs/Web/API/Canvas_API/Tutorial/Advanced_animations">Advanced animations</a></dt>
 <dd>高度なアニメーション技術と物の動きについて見ていきます。</dd>
</dl>

<p>{{PreviousNext("Web/API/Canvas_API/Tutorial/Compositing", "Web/API/Canvas_API/Tutorial/Advanced_animations")}}</p>