# add-youtube-to-the-page
需要实现的最终效果：当我们向页面插入，单个或者多个youtube视频时，期望可以在移动端达成，全屏，自动播放的效果。
首先是实现全屏，这个很简单，获取设备视口的宽度和高度，并将值分别赋予iframe标签,这样就能实现视频全屏的效果。
下面重点研究，在不点击播放器的情况下，实现视频自动播放。
思路1.使用html5的video标签，利用javascript的play()方法，很容易实现在不点击播放器本身播放按钮的情况下，视频自动播放。但是，把视频放在自己的服务器上太占带宽，通常做法是将视频放在youtube上,再利用iframe引用。这样就按照思路1来达成想要的效果，接下来请看思路2和思路3.
思路2.传统替换视屏的方法，改变iframe的src地址即可，以下是test代码。
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html lang="en">
<head>
	<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
	<title>iframe</title>
</head>
<body style="position:relative;">
<div class="main">
    <a href="javascript:" style="width:200px;height:100px;display:inline-block;background: #ccc;" id="video"></a>
    <a href="javascript:" style="width:200px;height:100px;display:inline-block;background: #14b8e2;" id="video2"></a>
    <a href="javascript:" title="Close" class="ficons close" style="position:fixed;right:100%;top:78px;z-index:3001;display:none;color:#000;" id="fvclose">关闭按钮</a>
</div>
<iframe  src="" frameborder=0 allowfullscreen="true"  scrolling=no allowtransparency class="box" width="320" height="400"  autoplay="1"></iframe>
<script src="js/jquery.min.js"></script>
<script>
	var videoWidth=$(window).width()+"px";
	var videoHeight=$(window).height()+"px";
	$("#video2").click(function(){
		$(".close").css({"display":"block"});
		$(".box").attr({"src":"https://www.youtube.com/embed/V6Ei4aYpl04?rel=0&showinfo=0&autoplay=1&loop=1&playlist=V6Ei4aYpl04"});
		$(".box").css({"width":videoWidth,"height":videoHeight});
	});
	$(".close").click(function(){
		$(this).css({"display":"none"});
		$(".box").attr({"src":""});
	});
	$("#video").click(function(){
		$(".close").css({"display":"block"});
		$(".box").attr({"src":"https://www.youtube.com/embed/O6uSkpCNLjk?rel=0&showinfo=0&autoplay=1&loop=1&playlist=O6uSkpCNLjk"});
		$(".box").css({"width":videoWidth,"height":videoHeight});
	});
</script>
</body>
</html>
思路2的结果是：在pc端很容易实现自动播放，但是在移动端，视频在加载完成后不能自动播放，必须点击播放器后视频才能播放。
在看看思路上3：使用youtube api YouTube IFrame API  当一个页面有一个视频需要播放时，能够实现自动播放，但是当一个页面同时出现两个及以上视频需要播放时，会出现一些问题。 详情可以阅读资料https://developers.google.com/youtube/iframe_api_reference#Requirements
实例1：一个页面播放一个视频。能够顺利实现自动播放。
 <div id="player" style="position:fixed;top:0px;left:0px;z-index:3000;"></div>
<script type="text/javascript">
 var tag = document.createElement('script');
 var videoWidth=$j(window).width()+"px";
 var videoHeight=$j(window).height()+"px";
      $j(".aw01btn02").click(function(){
        $j("#player").css({"display":"block","width":videoWidth,"height":videoHeight});  
          tag.src = "https://www.youtube.com/iframe_api";   
          $j(this).css({"display":"none"});
          $j(".fvclose").css({"display":"block"});      
          player.playVideo();  
      });
      $j(".fvclose").click(function(){
          $j("#player").fadeOut(500); 
          $j(this).css({"display":"none"});
          $j(".aw01btn02").css({"display":"block"});      
          player.stopVideo();  
      })
      var firstScriptTag = document.getElementsByTagName('script')[0];
      firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
      var player;
      function onYouTubeIframeAPIReady() {
        player = new YT.Player('player', {
          videoId: '0rVfVW79rVg',
          events: {
            'onReady': onPlayerReady,
          }
        });
      }
      function onPlayerReady(event) {
        event.target.playVideo();
      }
      var done = false;
      function stopVideo() {
        player.stopVideo();
      }
</script>
实例2：一个页面出现两个及以上视频时。虽然使用了列表队列函数loadPlaylist，插入播放列表后调用playVideoAt()函数，但是还是会出现问题。暂时得不到解决。
<div id="player" style="position:fixed;top:0px;left:0px;z-index:3000;"></div>
<script type="text/javascript">
    var $j=jQuery.noConflict();
    var tag = document.createElement('script');
    var videoWidth=$j(window).width()+"px";
    var videoHeight=$j(window).height()+"px";
    var firstScriptTag = document.getElementsByTagName('script')[0];
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
    var player;
    function onYouTubeIframeAPIReady() {
        player = new YT.Player('player', {
            videoId: 'O6uSkpCNLjk',
            loadPlaylist:{
                listType:'playlist',
                list:['O6uSkpCNLjk,V6Ei4aYpl04'],
                index:parseInt(0),
                suggestedQuality:'small'
            },
            events: {
                'onReady': onPlayerReady
            }
        });
    };
    function onPlayerReady(event) {
        event.target.loadPlaylist(['O6uSkpCNLjk,V6Ei4aYpl04']);
        event.target.loadVideoById({
            videoId: O6uSkpCNLjk,
            endSeconds: 2
        });
                }
    var done = false;
    function stopVideo() {
        player.stopVideo();
    };
    $j(".aw01btn").click(function(){
        $j("#player").css({"display":"block","width":videoWidth,"height":videoHeight});
        tag.src = "https://www.youtube.com/iframe_api";
        $j(this).css({"display":"none"});
        $j(".fvclose").css({"display":"block"});
        player.playVideoAt(0);
    });
    $j(".fvclose").click(function(){
        $j("#player").fadeOut(500);
        $j(this).css({"display":"none"});
        $j(".aw01btn").css({"display":"block"});
        player.stopVideo();
    });
    $j(".aw01btn02").click(function(){
        $j("#player").css({"display":"block","width":videoWidth,"height":videoHeight});
        tag.src = "https://www.youtube.com/iframe_api";
        $j(this).css({"display":"none"});
        $j(".fvclose02").css({"display":"block"});
        player.playVideoAt(1);
    });
    $j(".fvclose02").click(function(){
        $j("#player").fadeOut(500);
        $j(this).css({"display":"none"});
        $j(".aw01btn02").css({"display":"block"});
        player.stopVideo();
    });
    </script>
    最后的结论是在页面只有一个视频时，利用youtube api YouTube IFrame API 实现自动全屏播放，出现两个及以上视频，只能全屏不自动播放，
    必须增加用户和播放器的交互，如有点击播放器的行为后，才能播放视频。
    
