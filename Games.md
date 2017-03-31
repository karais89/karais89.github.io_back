---
layout: page
title: Games
permalink: /games/
---
<script src="http://mattstow.com/experiment/responsive-image-maps/jquery.rwdImageMaps.min.js"></script>
<script>
  (function($) {
    $(document).ready(function(e) {
      $('img[usemap]').rwdImageMaps();
      $("#img").width("100%");
    });
  })(jQuery);
</script>
<style type="text/css">
	img[usemap] {
		border: none;
		height: auto;
		max-width: 100%;
		width: auto;
	}
</style>

<div class="contents">
  <img src="/images/cotton_pies_banner.jpg" width="800" height="600" alt="Cotton Pies 소개" usemap="#eventMap" />
  <map name="eventMap">
    <area shape="rect" coords="135,480,375,555" href="http://goo.gl/hv4gdS" alt="Google Play Store" target="_blank">
    <area shape="rect" coords="420,480,660,555" href="http://goo.gl/WKbHFS" alt="App Store" target="_blank">
  </map>
</div>