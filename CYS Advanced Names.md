# Naming Page

Add one "Accept" link that leads to the next page.

```
<input id="playerName" type="text">
```

# Naming Page "Accept" Link Script

Return to the naming page if the first digit is empty.

```
IF %N0 = 0 THEN $DEST := @NONE
```

# Subsequent Pages

To insert the name into the story, use `$$NAME$$`.

# Global Page Script

IDK I might change some stuff later. Some of the variables can be changed to fit your game; they're the ones with comments next to them.

```
$PAGETEXT := "<script>
// Polyfills
'function'!=typeof Object.assign&&Object.defineProperty(Object,'assign',{value:function(t,e){'use strict';if(null==t)throw new TypeError('Cannot convert undefined or null to object');for(var r=Object(t),n=1;n<arguments.length;n++){var o=arguments[n];if(null!=o)for(var c in o)Object.prototype.hasOwnProperty.call(o,c)&&(r[c]=o[c])}return r},writable:!0,configurable:!0}),Object.keys||(Object.keys=function(){'use strict';var t=Object.prototype.hasOwnProperty,e=!{toString:null}.propertyIsEnumerable('toString'),r=['toString','toLocaleString','valueOf','hasOwnProperty','isPrototypeOf','propertyIsEnumerable','constructor'],n=r.length;return function(o){if('function'!=typeof o&&('object'!=typeof o||null===o))throw new TypeError('Object.keys called on non-object');var c,i,l=[];for(c in o)t.call(o,c)&&l.push(c);if(e)for(i=0;i<n;i++)t.call(o,r[i])&&l.push(r[i]);return l}}()),Object.entries||(Object.entries=function(t){for(var e=Object.keys(t),r=e.length,n=new Array(r);r--;)n[r]=[e[r],t[e[r]]];return n}),void 0===RegExp.prototype.flags&&Object.defineProperty(RegExp.prototype,'flags',{configurable:!0,get:function(){return this.toString().match(/[gimuy]*$/)[0]}});
</script><script>
var filter = /^[-']|[^-'a-zA-Z\xc0-\xd6\xd8-\xf6\xf8-\xff]/, // the regex defining what to filter out of the name (can be set to null for no filter)
    input = 'playerName', // id of the <input> element
    length = 12, // max length of the name
    playerName = '',
    prefix = 'N', // prefix of the name variables (must begin with [A-Z])
    regex = /\$\$NAME\$\$/i, // the regex to be replaced with the name
    ss = [],
    ssVariables = {};

document.addEventListener('DOMContentLoaded', function() {
    var globalFilter = filter instanceof RegExp ? new RegExp(filter.source, filter.flags.replace(/g|^/i, 'g')) : null,
        globalRegex = new RegExp(regex.source, regex.flags.replace(/g|^/i, 'g')),
        nameInput = document.querySelector('input#' + input);
    ss = atob(document.querySelector('input[name=\'SS\']').value).split('|');
    ssVariables = JSON.parse('{' + ss.pop().replace(/[A-Z][A-Z0-9]*/g, '\42$&\42').replace(/,/g, ':').replace(/;/g, ',') + '}');

    for (var i = 0; i < length; i++) {
        var code = ssVariables[prefix + i];
        if (code) playerName += String.fromCharCode(code);
    }

    if (nameInput) {
        nameInput.maxLength = length;
        nameInput.addEventListener('input', function() {
            var obj = {},
                val = this.value;
            if (filter) this.value = val = val.replace(filter, '');
            for (var i = 0; i < length; i++) {
                obj[prefix + i] = val.charCodeAt(i) || 0;
            }
            updateVariables(obj);
        });
    }

    var names = [].slice.call(document.querySelectorAll('.dark1border + div > div, .dark1border + div > div *')).reduce(function(a, e) {
        return a.concat([].slice.call(e.childNodes))
    }, []).filter(function(e) {
        return (e.nodeType == 3 && e.nodeValue.trim() && regex.test(e.data))
    });
    for (var i = 0; i < names.length; i++) {
        var div = document.createElement('div'),
            node = names[i];
        div.innerHTML = node.data.replace(globalRegex, playerName);
        while (div.firstChild) {
            node.parentNode.insertBefore(div.firstChild, node);
        }
        node.parentNode.removeChild(node);
    }
});

updateVariables = function(obj) {
    Object.assign(ssVariables, obj);
}

applySS = function() {
    document.querySelector('input[name=\'SS\']').value = btoa(
        ss.concat(Object.entries(ssVariables).map(function(e) {
            return e.join(',');
        }).join(';'))
    .join('|'));
}

PostBack = function(action, value) {
    applySS();
    var frm = getEl('pbForm');
    frm.pbAction.value = action ? action : '';
    frm.pbValue.value = value ? value : '';
    frm.submit();
    return false;
}
</script>" + $PAGETEXT
```
