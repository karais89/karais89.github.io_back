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
    <img src="/images/cotton_pies_banner.jpg" width="800" height="874" alt="Cotton Pies Banner" usemap="#eventMap" />
    <map name="eventMap">
        <area shape="rect" coords="290,440,340,490" href="https://twitter.com/lon_peach" alt="Twitter icon" target="_blank">
        <area shape="rect" coords="375,440,425,490" href="https://www.facebook.com/CottonPies-1749556838593765/" alt="Facebook icon" target="_blank">
        <area shape="rect" coords="455,440,505,490" href="https://www.instagram.com/cotton_pies/" alt="instagram icon" target="_blank">        
        <area shape="rect" coords="135,520,375,600" href="http://goo.gl/hv4gdS" alt="Google Play Store Button" target="_blank">
        <area shape="rect" coords="420,520,660,600" href="http://goo.gl/WKbHFS" alt="App Store Button" target="_blank">
    </map>
</div>