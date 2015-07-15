/**
 * Created by hWX229431 on 2015/7/8.
 */
;(function ($) {
    var remBase = 100;
    var rem = function (pxVal, base) {
        var fontSize = base || parseFloat(document.documentElement.style.fontSize);
        return pxVal / fontSize;
    };

    var px = function (remVal, base) {
        var fontSize = base || parseFloat(document.documentElement.style.fontSize);
        return remVal * fontSize;
    };

    $.fn.sliderList = function (settings) {
        //default settings
        var defaults = {
            type: 'normal',     // 'normal', 'special'
            size: '16x9',       //  '16x9', '5x7'
            supportDrag: true,
            selector: '.slider-component',    // html like this: <div class='slider-component'><ul><li></li></ul></div>
            viewPortWidth: '15.20rem',
            contentWidth: '3.65rem',
            contentHeight: '2.05rem',
            margin: '0.20rem'
        };

        var setting = $.extend(defaults, settings);
        var $container = $(this).find(setting.selector);
        var $parent = $(this).css({
            position: 'relative'
        });
        var $scrollEle = $container.find('ul');
        var isDrag = true;
        //set view port width & content width
        if (setting.type === 'special') {
            setting.viewPortWidth = '11.35rem';
            if(setting.size === '5x7'){
                setting.viewPortWidth = '12.50rem';
            }
        }
        if (setting.size === '5x7') {
            setting.contentWidth = '2.56rem';
            setting.contentHeight = '3.67rem';
        }
        //set css style
        $(this).css({
            width: '15.20rem'
        });
        $container.css({
            width: setting.viewPortWidth,
            overflow: 'hidden',
            position: 'relative',
            'z-index': 1,
            float: 'right'
        });
        if(setting.type === 'special'){
            $container.css({
                'margin-top': '0.6rem'
            });
            if(setting.size === '16x9'){
                $(this).css({
                    height: '4rem'
                });
            }else if(setting.size === '5x7'){
                $(this).css({
                    height: '5.1rem'
                });
            }
        }
        $scrollEle.css({
            position: 'relative',
            left: '0',
            top: '0',
            'list-style': 'none',
            'width': '9999999px'
        });
        $container.find('ul li').css({
            position: 'relative',
            width: setting.contentWidth,
            float: 'left',
            'margin-left': setting.margin
        });
        $container.find('ul li img').css({
            width: setting.contentWidth,
            height: setting.contentHeight
        });
        $container.find('ul li:first-child').css({
            'margin-left': 0
        });
        //add scroll btn
        var totalWidthInit = $scrollEle.find('li').length * (($scrollEle.find('li').width() + px(parseFloat(setting.margin)))) - px(parseFloat(setting.margin));
        var $leftArrow = $('<div class="left-arrow">').css({
            position: 'absolute',
            top: '50%',
            left: '0',
            cursor: 'pointer',
            transform: 'translate3d(0,-50%,0)',
            width: '0.675rem',
            height: '1.32rem',
            background: 'url(res/en/img/left-normal.png) no-repeat',
            'background-size': '100% 100%',
            'z-index': 99
        }).appendTo($container).hide();
        var $rightArrow = $('<div class="right-arrow">').css({
            position: 'absolute',
            top: '50%',
            right: '0',
            cursor: 'pointer',
            transform: 'translate3d(0,-50%,0)',
            width: '0.675rem',
            height: '1.32rem',
            background: 'url(res/en/img/right-normal.png) no-repeat',
            'background-size': '100% 100%',
            'z-index': 99
        }).appendTo($container);
        if(totalWidthInit <= $container.width()){
            $rightArrow.hide();
        }else {
            $rightArrow.show();
        }

        $leftArrow.click(function (e) {
            e.preventDefault();
            e.stopPropagation();
            isDrag = false;
            scroll(-1);
        });
        $rightArrow.click(function (e) {
            e.preventDefault();
            e.stopPropagation();
            isDrag = false;
            scroll(1);
        });

        var transitionElement = function($ele){
            $ele.css({
                '-webkit-transition': 'left ease-in .2s',
                '-moz-transition': 'left ease-in .2s',
                '-ms-transition': 'left ease-in .2s',
                '-o-transition': 'left ease-in .2s',
                transition: 'left ease-in .2s'
            });
        };

        var removeTransition = function($ele){
            $ele.css({
                '-webkit-transition': 'none',
                '-moz-transition': 'none',
                '-ms-transition': 'none',
                '-o-transition': 'none',
                transition: 'none'
            });
        };

        var checkToHideButton = function (leftPos, totalWidth, containerWidth) {
            if(leftPos + containerWidth >= totalWidth){
                $rightArrow.hide();
            }else {
                $rightArrow.show();
            }
            if(leftPos === 0){
                $leftArrow.hide();
            }else {
                $leftArrow.show();
            }
        };

        var hideButtons = function(){
            $leftArrow.hide();
            $rightArrow.hide();
        };

        var scroll = function (direction) {
            var leftPos = Math.abs(parseFloat($scrollEle.css('left')));
            var totalWidth = $scrollEle.find('li').length * (($scrollEle.find('li').width() + px(parseFloat(setting.margin)))) - px(parseFloat(setting.margin));
            var showNum = Math.floor($container.width() / $scrollEle.find('li').width());
            var isMore = $container.width() % $scrollEle.find('li').width() === 0;
            if (direction > 0) {
                isDrag = false;
                //scroll to left
                if (leftPos + $container.width() >= totalWidth) {
                    //no scroll
                    $rightArrow.hide();
                } else {
                    hideButtons();
                    transitionElement($scrollEle);
                    if (leftPos + $container.width() > totalWidth - $container.width()) {
                        //last content
                        //$scrollEle.stop(true,true).animate({
                        //    left: -1 * (totalWidth - $container.width()) + 'px'
                        //}, 400, function(){
                        //    checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                        //    removeEvent();
                        //});
                        console.error('go left');
                        $scrollEle.css({
                            left: -1 * (totalWidth - $container.width()) + 'px'
                            //left: -1 * (totalWidth - $container.width())+'px'
                        });
                        setTimeout(function(){
                            checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                            removeEvent();
                        }, 200);
                    }else{
                        //first content
                       //$scrollEle.stop(true,true).animate({
                       //     left: -1 * (leftPos + showNum * ($scrollEle.find('li').width() + px(parseFloat(setting.margin)))) + 'px'
                       //}, 400, function(){
                       //     checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                       //    removeEvent();
                       //});
                        console.error('go left');
                        $scrollEle.css({
                            left: -1 * (leftPos + showNum * ($scrollEle.find('li').width() + px(parseFloat(setting.margin)))) + 'px'
                            //transform: 'translate('+ (-1 * (leftPos + showNum * ($scrollEle.find('li').width() + px(parseFloat(setting.margin))))) +'px)'
                        });
                        setTimeout(function(){
                            checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                            removeEvent();
                        }, 200);
                    }
                }
            } else {
                //scroll to right
                if (leftPos === 0) {
                    //no scroll
                    $leftArrow.hide();
                } else {
                    hideButtons();
                    if (leftPos - $container.width() <= 0) {
                        //last content
                        console.error('go right');
                        $scrollEle.stop(true,true).animate({
                            left: 0 + 'px'
                        }, 400, function(){
                            checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                            removeEvent();
                        });
                        //$scrollEle.css({
                        //    //left: 0 + 'px'
                        //    transform: 'translate(0)'
                        //});
                        //setTimeout(function(){
                        //    checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                        //    removeEvent();
                        //}, 200);

                    }else {
                        //normal content
                        console.error('go right');
                        $scrollEle.stop(true,true).animate({
                            left: -1 * (leftPos - showNum * ($scrollEle.find('li').width() + px(parseFloat(setting.margin)))) + 'px'
                        }, 400, function(){
                            checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                            removeEvent();
                        });

                        //$scrollEle.css({
                        //    //left: -1 * (leftPos - showNum * ($scrollEle.find('li').width() + px(parseFloat(setting.margin)))) + 'px'
                        //    transform: 'translate('+-1 * (leftPos - showNum * ($scrollEle.find('li').width() + px(parseFloat(setting.margin))))+'px)'
                        //});
                        //setTimeout(function(){
                        //    checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                        //    removeEvent();
                        //}, 200);
                    }
                }
            }
        };

        //bind drag event
        var ox, mx, leftPos;
        var removeEvent = function(){
            isDrag = true;
            $(document).unbind('mousemove');
            $(document).unbind('mouseup');
        };
        $container.unbind().bind('mousedown', function(eDown){
            console.error('mousedown');
            if(!isDrag){
                $(document).unbind('mousemove');
                $(document).unbind('mouseup');
                return;
            }
            leftPos = parseFloat($scrollEle.css('left'));
            eDown.preventDefault();
            eDown.stopPropagation();
            ox = eDown.screenX;
            var $ele = $(this);
            $(document).unbind('mousemove').bind('mousemove', function(eMove){
                console.error('mousemove');
                mx = eMove.screenX;
                removeTransition($scrollEle);
                $scrollEle.css({
                    left: leftPos + (mx-ox) + 'px'
                });
            });
            $(document).unbind('mouseup').bind('mouseup', function(eUp){
                console.error('mouseup');
                $(document).unbind('mousemove');
                transitionElement($scrollEle);
                var CurrentLeftPos = parseFloat($scrollEle.css('left'));
                var totalWidth = $scrollEle.find('li').length * (($scrollEle.find('li').width() + px(parseFloat(setting.margin)))) - px(parseFloat(setting.margin));
                //check if reached the ledge
                if(CurrentLeftPos > 0){
                    $scrollEle.stop(true,true).animate({
                        left: 0 + 'px'
                    }, 400, function(){
                        checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                        removeEvent();
                    });
                    //$scrollEle.css({
                    //    left: 0 + 'px'
                    //});
                    //setTimeout(function(){
                    //    checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                    //    removeEvent();
                    //}, 200);
                }else if(CurrentLeftPos < $ele.width() - totalWidth){
                    $scrollEle.stop(true, true).animate({
                        left: $ele.width() - totalWidth  + 'px'
                    }, 400, function(){
                        checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                        removeEvent();
                    });
                    //$scrollEle.css({
                    //    left: $ele.width() - totalWidth  + 'px'
                    //});
                    //setTimeout(function(){
                    //    checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                    //    removeEvent();
                    //}, 200);
                }else {
                    if(Math.abs(CurrentLeftPos) %  ($scrollEle.find('li').width() + px(parseFloat(setting.margin))) !== 0){
                        var num = parseInt(Math.abs(CurrentLeftPos) / ($scrollEle.find('li').width() + px(parseFloat(setting.margin))), 10);
                        $scrollEle.stop(true,true).animate({
                            left: -1*((num+1) * ($scrollEle.find('li').width() + px(parseFloat(setting.margin))))  + 'px'
                        }, 400, function(){
                            checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                            removeEvent();
                        });
                        //$scrollEle.css({
                        //    left: -1*((num+1) * ($scrollEle.find('li').width() + px(parseFloat(setting.margin))))  + 'px'
                        //});
                        //setTimeout(function(){
                        //    checkToHideButton(Math.abs(parseFloat($scrollEle.css('left'))), totalWidth, $container.width());
                        //    removeEvent();
                        //}, 200);
                    }
                }
            });
        });
    }
})(jQuery);
