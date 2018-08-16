# MiniUI

## 自定义Vtype

```js
/*自定义vtype*/
mini.VTypes["englishErrorText"] = "请输入英文";
mini.VTypes["english"] = function (v) {
	var re = new RegExp("^[a-zA-Z\_]+$");
	if (re.test(v)) 
        return true;
	return false;
}
```

