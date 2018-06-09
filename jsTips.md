# JAVASCRIPT TIPS
-----
1. 检测平台的字节顺序（大/小端法），大部分浏览器采用小端法：
    
        // true 为小端法
        var littleEndian = (function() {
            var buffer = new ArrayBuffer( 2 );
            new DataView( buffer ).setInt16( 0, 256, true );
            return new Int16Array( buffer )[0] === 256;
        })();
