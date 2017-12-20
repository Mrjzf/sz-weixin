window.getSign = getSign;

function getSign(s){
    if(s=='undefined' || s ==null)
        return ;

    return bytes2Str(stringToBytes(hex_md5(s)));
}

function stringToBytes ( str ) {
    var ch, st, re = [];
    for (var i = 0; i < str.length; i++ ) {
        ch = str.charCodeAt(i);  // get char
        st = [];                 // set up "stack"
        do {
            st.push( ch & 0xFF );  // push byte to stack
            ch = ch >> 8;          // shift value down by 1 byte
        }
        while ( ch );
        // add stack contents to result
        // done because chars have "wrong" endianness
        re = re.concat( st.reverse() );
    }
    // return an array of bytes
    return re;
}
function bytes2Str(arr){
    var str = "";
    for(var i=0; i<arr.length; i++){
        var tmp = arr[i].toString(16);
        if(tmp.length == 1){
            tmp = "0" + tmp;
        }
        str += tmp;
    }
    return str;
}
