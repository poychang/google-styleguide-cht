#Javascript格式規則#

##命名##
通常來說，使用 `functionNamesLikeThis` ， `variableNamesLikeThis` ， `ClassNamesLikeThis` ， `EnumNamesLikeThis` ， `methodNamesLikeThis` ， `CONSTANT_VALUES_LIKE_THIS` ， `foo.namespaceNamesLikeThis.bar` 和 `ilenameslikethis.js` 這種格式的命名方式。

##屬性和方法##
*	私有屬性和方法應該以下劃線開頭命名。
*	保護屬性和方法應該以無下劃線開頭命名（像公共屬性和方法一樣）。

了解更多關於私有成員和保護成員的信息，請閱讀[可見性](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Visibility__private_and_protected_fields_)部分。

##方法和函數參數##
可選函數參數以 opt_ 開頭。

參數數目可變的函數應該具有以 `var_args` 命名的最後一個參數。你可能不會在代碼裡引用 `var_args` ；使用 `arguments` 對象。

可選參數和數目可變的參數也可以在註釋 `@param` 中指定。儘管這兩種慣例都被編譯器接受，但更加推薦兩者一起使用。

##getter和setter##
EcmaScript 5 不鼓勵使用屬性的getter和setter。然而，如果使用它們，那麼getter就不要改變屬性的可見狀態。

	/**
	*錯誤--不要這樣做.
	*/
	var foo = { get next() { return this.nextId++; } };
	};

###存取函數###
屬性的getter和setter方法不是必需的。然而，如果使用它們，那麼讀取方法必須以 `getFoo()` 命名，並且寫入方法必須以 `setFoo(value)` 命名。（對於布爾型的讀取方法，也可以使用 `isFoo()` ，並且這樣往往聽起來更自然。）

###命名空間###
JavaScript沒有原生的對封裝和命名空間的支持。

全局命名衝突難以調試，並且當兩個項目嘗試整合的時候可能引起棘手的問題。為了能共享共用的JavaScript代碼，我們採用一些約定來避免衝突。

為全局代碼使用命名空間在全局範圍內總是使用唯一的項目或庫相關的偽命名空間進行前綴標識。如果你正在進行“Project Sloth”項目，一個合理的偽命名空間為 `sloth.*` 。

	var sloth = {};
	sloth.sleep = function() {
	  ...
	};

很多JavaScript庫，包括[the Closure Library](https://developers.google.com/closure/library/?csw=1)和[Dojo toolkit](http://dojotoolkit.org/)給你高級功能來聲明命名空間。保持你的命名空間聲明一致。

	goog.provide('sloth');
	sloth.sleep = function() {
	  ...
	};

###尊重命名空間###
所有權當選擇一個子命名空間的時候，確保父命名空間知道你在做什麼。如果你開始了一個為sloths創建hats的項目，確保Sloth這一組命名空間知道你在使用 `sloth.hats` 。

###外部代碼和內部代碼使用不同的命名空間###
“外部代碼”指的是來自你的代碼庫外並獨立編譯的代碼。內部名稱和外部名稱應該嚴格區分開。如果你正在使用一個能調用 `foo.hats.*` 中的東西的外部庫，你的內部代碼不應該定義 `foo.hats.*` 中的所有符號，因為如果其他團隊定義新符號就會把它打破。

	foo.require('foo.hats');
	/**
	*錯誤--不要這樣做。
	* @constructor
	* @extends {foo.hats.RoundHat}
	*/
	foo.hats.BowlerHat = function() {
	};

如果你在外部命名空間中需要定義新的API，那麼你應該明確地導出且僅導出公共的API函數。為了一致性和編譯器更好的優化你的內部代碼，你的內部代碼應該使用內部API的內部名稱調用它們。

	foo.provide('googleyhats.BowlerHat');
	foo.require('foo.hats');
	/**
	* @constructor
	* @extends {foo.hats.RoundHat}
	*/
	googleyhats.BowlerHat = function() {
  	...
	};
	goog.exportSymbol('foo.hats.BowlerHat', googleyhats.BowlerHat);

###為長類型的名稱提供別名提高可讀性###
如果對完全合格的類型使用本地別名可以提高可讀性，那麼就這樣做。本地別名的名稱應該符合類型的最後一部分。

	/**
	* @constructor
	*/
	some.long.namespace.MyClass = function() {
	};
	/**
	* @param {some.long.namespace.MyClass} a
	*/
	some.long.namespace.MyClass.staticHelper = function(a) {
	  ...
	};
	myapp.main = function() {
	  var MyClass = some.long.namespace.MyClass;
	  var staticHelper = some.long.namespace.MyClass.staticHelper;
	  staticHelper(new MyClass());
	};

不要為命名空間起本地別名。命名空間應該只能使用[goog.scope](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#goog-scope)命名別名。

	myapp.main = function() {
	  var namespace = some.long.namespace;
	  namespace.MyClass.staticHelper(new namespace.MyClass());
	};

避免訪問一個別名類型的屬性，除非它是一個枚舉。

	/** @enum {string} */
	some.long.namespace.Fruit = {
	  APPLE: 'a',
	  BANANA: 'b'
	};
	myapp.main = function() {
	  var Fruit = some.long.namespace.Fruit;
	  switch (fruit) {
	    case Fruit.APPLE:
	      ...
	    case Fruit.BANANA:
	      ...
	  }
	};

	myapp.main = function() {
	  var MyClass = some.long.namespace.MyClass;
	  MyClass.staticHelper(null);
	};

永遠不要在全局環境中創建別名。只在函數體內使用它們。

###文件名###
為了避免在大小寫敏感的平台上引起混淆，文件名 ​​應該小寫。文件名 ​​應該以 .js 結尾，並且應該不包含除了 - 或 _ （相比較 _ 更推薦 - ）以外的其它標點。

##自定義toString() 方法##
必須確保無誤，並且無其他副作用。

你可以通過自定義 `toString()` 方法來控制對像如何字符串化他們自己。這沒問題，但是你必須確保你的方法執行無誤，並且無其他副作用。如果你的方法沒有達到這個要求，就會很容易產生嚴重的問題。比如，如果 `toString()` 方法調用一個方法產生一個斷言，斷言可能要輸出對象的名稱，就又需要調用 `toString()` 方法。

##延時初始化##
可以使用。

並不總在變量聲明的地方就進行變量初始化，所以延時初始化是可行的。

##明確作用域##
時常。

經常使用明確的作用域加強可移植性和清晰度。例如，在作用域鏈中不要依賴 window 。你可能想在其他應用中使用你的函數，這時此 window 就非彼 window 了。

##代碼格式##
我們原則上遵循C++格式規範，並且進行以下額外的說明。

大括號由於隱含分號的插入，無論大括號括起來的是什麼，總是在同一行上開始你的大括號。例如：

	if (something) {
	  // ...
	} else {
	  // …
	}

###數組和對像初始化表達式###
當單行數組和對像初始化表達式可以在一行寫開時，寫成單行是允許的。

	var arr = [1, 2, 3]; //之後無空格[或之前]
	var obj = {a: 1, b: 2, c: 3}; //之後無空格[或之前]

多行數組和對像初始化表達式縮進兩個空格，括號的處理就像塊一樣單獨成行。

	//對像初始化表達式
	var inset = {
	  top: 10,
	  right: 20,
	  bottom: 15,
	  left: 12
	};
	//數組初始化表達式
	this.rows_ = [
	  '"Slartibartfast" <fjordmaster@magrathea.com>',
	  '"Zaphod Beeblebrox" <theprez@universe.gov>',
	  '"Ford Prefect" <ford@theguide.com>',
	  '"Arthur Dent" <has.no.tea@gmail.com>',
	  '"Marvin the Paranoid Android" <marv@googlemail.com>',
	  'the.mice@magrathea.com'
	];
	//在方法調用中使用
	goog.dom.createDom(goog.dom.TagName.DIV, {
	  id: 'foo',
	  className: 'some-css-class',
	  style: 'display:none'
	}, 'Hello, world!');

長標識符或值在對齊的初始化列表中存在問題，所以初始化值不必對齊。例如：

	CORRECT_Object.prototype = {
	  a: 0,
	  b: 1,
	  lengthyName: 2
	};

不要像這樣：

	WRONG_Object.prototype = {
	  a : 0,
	  b : 1,
	  lengthyName: 2
	};

###函數參數###
如果可能，應該在同一行上列出所有函數參數。如果這樣做將超出每行80個字符的限制，參數必須以一種可讀性較好的方式進行換行​​。為了節省空間，在每一行你可以盡可能的接近80個字符，或者把每一個參數單獨放在一行以提高可讀性。縮進可能是四個空格，或者和括號對齊。下面是最常見的參數換行形式：

	// 四個空格，每行包括80個字符。適用於非常長的函數名，
	// 重命名不需要重新縮進，佔用空間小。
	goog.foo.bar.doThingThatIsVeryDifficultToExplain = function(
	    veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
	    tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
	    // ...
	};
	//四個空格，每行一個參數。適用於長函數名，
	// 允許重命名，並且強調每一個參數。
	goog.foo.bar.doThingThatIsVeryDifficultToExplain = function(
	    veryDescriptiveArgumentNumberOne,
	    veryDescriptiveArgumentTwo,
	    tableModelEventHandlerProxy,
	    artichokeDescriptorAdapterIterator) {
	    // ...
	};
	// 縮進和括號對齊，每行80字符。看上去是分組的參數，
	// 佔用空間小。
	function foo(veryDescriptiveArgumentNumberOne, veryDescriptiveArgumentTwo,
	            tableModelEventHandlerProxy, artichokeDescriptorAdapterIterator) {
	    // ...
	}
	// 和括號對齊，每行一個參數。看上去是分組的並且
	// 強調每個單獨的參數。
	function bar(veryDescriptiveArgumentNumberOne,
	            veryDescriptiveArgumentTwo,
	            tableModelEventHandlerProxy,
	            artichokeDescriptorAdapterIterator) {
	    // ...
	}

當函數調用本身縮進，你可以自由地開始相對於原始聲明的開頭或者相對於當前函數調用的開頭，進行4個空格的縮進。以下都是可接受的縮進風格。

	if (veryLongFunctionNameA(
	        veryLongArgumentName) ||
	    veryLongFunctionNameB(
	    veryLongArgumentName)) {
	  veryLongFunctionNameC(veryLongFunctionNameD(
	      veryLongFunctioNameE(
	          veryLongFunctionNameF)));
	}

###匿名函數傳遞###
當在一個函數的參數列表中聲明一個匿名函數時，函數體應該與聲明的左邊緣縮進兩個空格，或者與function關鍵字的左邊緣縮進兩個空格。這是為了匿名函數體更加可讀（即不被擠在屏幕的右側）。

	prefix.something.reallyLongFunctionName('whatever', function(a1, a2) {
	  if (a1.equals(a2)) {
	    someOtherLongFunctionName(a1);
	  } else {
	    andNowForSomethingCompletelyDifferent(a2.parrot);
	  }
	});
	var names = prefix.something.myExcellentMapFunction(
	    verboselyNamedCollectionOfItems,
	    function(item) {
	      return item.name;
	    });

###使用goog.scope命名別名###
[goog.scope](https://docs.google.com/document/d/1ETFAuh2kaXMVL-vafUYhaWlhl6b5D9TOvboVg7Zl68Y/pub)可用於在使用[the Closure Library](https://developers.google.com/closure/library/?csw=1)的工程中縮短命名空間的符號引用。

每個文件只能添加一個 `goog.scope` 調用。始終將它放在全局範圍內。

開放的 `goog.scope(function() {` 調用必須在之前有一個空行，並且緊跟 `goog.provide` 聲明、 `goog.require` 聲明或者頂層的註釋。調用必須在文件的最後一行閉合。在scope聲明閉合處追加 `// goog.scope` 。註釋與分號間隔兩個空格。

和C++命名空間相似，不要在 `goog.scope` 聲明下面縮進。相反，從第0列開始。

只取不會重新分配給另一個對象（例如大多數的構造函數、枚舉和命名空間）的別名。不要這樣做：

	goog.scope(function() {
	var Button = goog.ui.Button;
	Button = function() { ... };
	...

別名必須和全局中的命名的最後一個屬性相同。

	goog.provide('my.module');
	goog.require('goog.dom');
	goog.require('goog.ui.Button');
	goog.scope(function() {
	var Button = goog.ui.Button;
	var dom = goog.dom;
	// Alias​​ new types after the constructor declaration.
	my.module.SomeType = function() { ... };
	var SomeType = my.module.SomeType;
	// Declare methods on the prototype as usual:
	SomeType.prototype.findButton = function() {
	  // Button as aliased above.
	  this.button = new Button(dom.getElement('my-button'));
	};
	...
	}); // goog.scope

###更多的縮進###
事實上，除了[初始化數組和對象](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Array_and_Object_literals)和傳遞匿名函數外，所有被拆開的多行文本應與之前的表達式左對齊，或者以4個（而不是2個）空格作為一縮進層次。

	someWonderfulHtml = '' +
	                    getEvenMoreHtml(someReallyInterestingValues​​, moreValues​​,
	                                    evenMoreParams, 'a duck', true, 72,
	                                    slightlyMoreMonkeys(0xfff)) +
	                    '';
	thisIsAVeryLongVariableName =
	    hereIsAnEvenLongerOtherFunctionNameThatWillNotFitOnPrevLine();
	thisIsAVeryLongVariableName = 'expressionPartOne' + someMethodThatIsLong() +
	    thisIsAnEvenLongerOtherFunctionNameThatCannotBeIndentedMore();
	someValue = this.foo(
	    shortArg,
	    'Some really long string arg - this is a pretty common case, actually.',
	    shorty2,
	    this.bar());
	if (searchableCollection(allYourStuff).contains(theStuffYouWant) &&
	    !ambientNotification.isActive() && (client.isAmbientSupported() ||
	                                        client.alwaysTryAmbientAnyways())) {
	  ambientNotification.activate();
	}

###空行###
使用新的空行來劃分一組邏輯上相關聯的代碼片段。例如：

	doSomethingTo(x);
	doSomethingElseTo(x);
	andThen(x);
	nowDoSomethingWith(y);
	andNowWith(z);

###二元和三元操作符###
操作符始終跟隨著前行, 這樣你就不用顧慮分號的隱式插入問題。否則換行符和縮進還是遵循其他谷歌規範指南。

	var x = a ? b : c; // All on one line if it will fit.
	// Indentation +4 is OK.
	var y = a ?
	    longButSimpleOperandB : longButSimpleOperandC;
	// Indenting to the line position of the first operand is also OK.
	var z = a ?
	        moreComplicatedB :
	        moreComplicatedC;

點號也應如此處理。

	var x = foo.bar().
	    doSomething().
	    doSomethingElse();

###括號###
只用在有需要的地方。

通常只在語法或者語義需要的地方有節制地使用。

絕對不要對一元運算符如 `delete` 、 `typeof` 和 `void` 使用括號或者在關鍵詞如 `return` 、 `throw` 和其他的（ `case` 、 `in` 或者 `new` ）之後使用括號。

###字符串###
使用 `'` 代替 `"` 。
使用單引號（ `'` ）代替雙引號（ `"` ）來保證一致性。當我們創建包含有HTML的字符串時這樣做很有幫助。

	var msg = 'This is some HTML';

###可見性（私有和保護類型字段）###
鼓勵使用 `@private` 和 `@protected`  JSDoc註釋。

我們建議使用JSDoc註釋 `@private` 和 `@protected` 來標識出類、函數和屬性的可見程度。

設置 `--jscomp_warning=visibility` 可令編譯器對可見性的違規進行編譯器警告。可見[封閉的編譯器警告](https://code.google.com/p/closure-compiler/wiki/Warnings)。

加了 `@private` 標記的全局變量和函數只能被同一文件中的代碼所訪問。

被標記為 `@private` 的構造函數只能被同一文件中的代碼或者它們的靜態和實例成員實例化。 `@private` 標記的構造函數可以被相同文件內它們的公共靜態屬性和 `instanceof` 運算符訪問。

全局變量、函數和構造函數不能註釋 `@protected` 。

	// 文件1
	// AA_PrivateClass_ 和AA_init_ 是全局的並且在同一個文件中所以能被訪問
	/**
	* @private
	* @constructor
	*/
	AA_PrivateClass_ = function() {
	};
	/** @private */
	function AA_init_() {
	  return new AA_PrivateClass_();
	}
	AA_init_();

標記 `@private` 的屬性可以被同一文件中的所有的代碼訪問，如果屬性屬於一個類，那麼所有自身含有屬性的類的靜態方法和實例方法也可訪問。它們不能被不同文件下的子類訪問或者重寫。

標記 `@protected` 的屬性可以被同一文件中的所有的代碼訪問，任何含有屬性的子類的靜態方法和實例方法也可訪問。

注意這些語義和C++、JAVA中private 和protected的不同，其許可同一文件中的所有代碼訪問的權限，​​而不是僅僅局限於同一類或者同一類層次。此外，不像C++中，子類不可重寫私有屬性。

	// File 1.
	/** @constructor */
	AA_PublicClass = function() {
	  /** @private */
	  this.privateProp_ = 2;
	  /** @protected */
	  this.protectedProp = 4;
	};
	/** @private */
	AA_PublicClass.staticPrivateProp_ = 1;
	/** @protected */
	AA_PublicClass.staticProtectedProp = 31;
	/** @private */
	AA_PublicClass.prototype.privateMethod_ = function() {};
	/** @protected */
	AA_PublicClass.prototype.protectedMethod = function() {};
	// File 2.
	/**
	* @return {number} The number of ducks we've arranged in a row.
	*/
	AA_PublicClass.prototype.method = function() {
	  // Legal accesses of these two properties.
	  return this.privateProp_ + AA_PublicClass.staticPrivateProp_;
	};
	// File 3.
	/**
	* @constructor
	* @extends {AA_PublicClass}
	*/
	AA_SubClass = function() {
	  // Legal access of a protected static property.
	  AA_PublicClass.staticProtectedProp = this.method();
	};
	goog.inherits(AA_SubClass, AA_PublicClass);
	/**
	* @return {number} The number of ducks we've arranged in a row.
	*/
	AA_SubClass.prototype.method = function() {
	  // Legal access of a protected instance property.
	  return this.protectedProp;
	};

注意在Javascript中，一個類（如 `AA_PrivateClass_` ）和其構造函數類型是沒有區別的。沒辦法確定一種類型是public而它的構造函數是private。（因為構造函數很容易重命名從而躲避隱私檢查）。

##JavaScript類型##
鼓勵和強制執行的編譯器。

JSDoc記錄類型時，要盡可能具體和準確。我們支持的類型是基於[EcmaScript 4規範](http://wiki.ecmascript.org/doku.php?id=spec:spec)。

###JavaScript類型語言###
ES4提案包含指定JavaScript類型的語言。我們使用JsDoc這種語言表達函數參數和返回值的類型。

隨著ES4提議的發展，這種語言已經改變了。編譯器仍然支持舊的語法類型，但這些語法已經被棄用了。


<table>
	<thead valign="bottom">
		<tr>
			<th>語法名稱</th>
			<th>語法</th>
			<th>描述</th>
			<th>棄用語法</th>
		</tr>
	</thead>
	<tbody valign="top">
		<tr>
			<td>原始類型</td>
			<td>在JavaScript中有5種原始類型： 
			<tt>{null}</tt> ， 
			<tt>{undefined}</tt> ， 
			<tt>{boolean}</tt> ， 
			<tt>{number}</tt> ，和 
			<tt>{string}</tt></td>
			<td>類型的名稱。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>實例類型</td>
			<td>
				<p>
				<tt>{Object}</tt> 實例對像或空。</p>
				<p>
				<tt>{Function}</tt> 一個實例函數或空。</p>
				<p>
				<tt>{EventTarget}</tt> 構造函數實現的EventTarget接口，或者為null的一個實例。</p>
			</td>
			<td>
				<p>一個實例構造函數或接口函數。構造函數是 
				<tt>@constructor</tt> JSDoc標記定義的函數 。接口函數是 
				<tt>@interface</tt> JSDoc標記定義的函數。</p>
				<p>默認情況下，實例類型將接受空。這是唯一的類型語法，使得類型為空。此表中的其他類型的語法不會接受空。</p>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>枚舉類型</td>
			<td>
			<tt>{goog.events.EventType}</tt> 字面量初始化對象的屬性之一 
			<tt>goog.events.EventType</tt> 。</td>
			<td>
				<p>一個枚舉必須被初始化為一個字面量對象，或作為另一個枚舉的別名,加注 
				<tt>@enum</tt> JSDoc標記。這個屬性是枚舉實例。 
				<a href="http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#enums">下面</a> 是枚舉語法的定義。</p>
				<p>請注意，這是我們的類型系統中為數不多的ES4規範以外的事情之一。</p>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>應用類型</td>
			<td>
				<p>
				<tt>{Array.&lt;string&gt;}</tt> 字符串數組。</p>
				<p>
				<tt>{Object.&lt;string, number&gt;}</tt> 一個對象，其中鍵是字符串，值是數字。</p>
			</td>
			<td>參數化類型，該類型應用一組參數類型。這個想法是類似於Java泛型。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>聯合類型</td>
			<td>
			<tt>{(number|boolean)}</tt> 一個數字或布爾值。</td>
			<td>
				<p>表明一個值可能有A型或B型。</p>
				<p>括號在頂層表達式可以省略，但在子表達式不能省略，以避免歧義。</p>
				<p>
					<tt>{number|boolean}</tt>
				</p>
				<p>
					<tt>{function(): (number|boolean)}</tt>
				</p>
			</td>
			<td>
			<tt>{(number,boolean)}</tt> ， 
			<tt>{(number||boolean)}</tt></td>
		</tr>
		<tr>
			<td>可為空的類型</td>
			<td>
				<p>
					<tt>{?number}</tt>
				</p>
				<p>一個數字或空。</p>
			</td>
			<td>空類型與任意其他類型組合的簡稱。這僅僅是語法糖（syntactic sugar）。</td>
			<td>
				<tt>{number?}</tt>
			</td>
		</tr>
		<tr>
			<td>非空類型</td>
			<td>
				<p>
					<tt>{!Object}</tt>
				</p>
				<p>一個對象，值非空。</p>
			</td>
			<td>從非空類型中過濾掉null。最常用於實例類型，默認可為空。</td>
			<td>
				<tt>{Object!}</tt>
			</td>
		</tr>
		<tr>
			<td>記錄類型</td>
			<td>
				<p>
					<tt>{{myNum: number, myObject}}</tt>
				</p>
				<p>給定成員類型的匿名類型。</p>
			</td>
			<td>
				<p>表示該值有指定的類型的成員。在這種情況下， 
				<tt>myNum</tt> 是 
				<tt>number</tt> 類型而 
				<tt>myObject</tt> 可為任何類型。</p>
				<p>注意花括號是語法類型的一部分。例如，表示一個數組對像有一個 
				<tt>length</tt> 屬性，你可以寫 
				<tt>Array.&lt;{length}&gt;</tt> 。</p>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>函數類型</td>
			<td>
				<p>
					<tt>{function(string, boolean)}</tt>
				</p>
				<p>一個函數接受兩個參數（一個字符串和一個布爾值），並擁有一個未知的返回值。</p>
			</td>
			<td>指定一個函數。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>函數返回類型</td>
			<td>
				<p>
					<tt>{function(): number}</tt>
				</p>
				<p>一個函數沒有參數並返回一個數字。</p>
			</td>
			<td>指定函數的返回類型。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>函數 
			<tt>this</tt> 類型</td>
			<td>
				<p>
					<tt>{function(this:goog.ui.Menu, string)}</tt>
				</p>
				<p>一個需要一個參數（字符串）的函數，執行上下文是 
				<tt>goog.ui.Menu</tt></p>
			</td>
			<td>指定函數類型的上下文類型。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>函數 
			<tt>new</tt> 類型</td>
			<td>
				<p>
					<tt>{function(new:goog.ui.Menu, string)}</tt>
				</p>
				<p>一個構造函數接受一個參數（一個字符串），並在使用「new」關鍵字時創建一個 
				<tt>goog.ui.Menu</tt> 新實例。</p>
			</td>
			<td>指定構造函數所構造的類型。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>可變參數</td>
			<td>
				<p>
					<tt>{function(string, ...[number]): number}</tt>
				</p>
				<p>一個函數，它接受一個參數（一個字符串），然後一個可變數目的參數，必須是數字。</p>
			</td>
			<td>指定函數的變量參數。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>可變參數（ 
			<tt>@param</tt> 註釋）</td>
			<td>
				<p>
					<tt>@param {...number} var_args</tt>
				</p>
				<p>帶註釋函數的可變數目參數。</p>
			</td>
			<td>指定帶註釋函數接受一個可變數目的參數。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>函數 
			<a href="http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#optional">可選參數</a></td>
			<td>
				<p>
					<tt>{function(?string=, number=)}</tt>
				</p>
				<p>一個函數，它接受一個可選的、可以為空的字符串和一個可選的數字作為參數。「=」只用於函數類型聲明。</p>
			</td>
			<td>指定函數的可選參數。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>函數 
			<a href="http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#optional">可選參數</a> （ 
			<tt>@param</tt> 註釋）</td>
			<td>
				<p>
					<tt>@param {number=} opt_argument</tt>
				</p>
				<p>
				<tt>number</tt> 類型的可選參數。</p>
			</td>
			<td>指定帶註釋函數接受一個可選的參數。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>所有類型</td>
			<td>
				<tt>{*}</tt>
			</td>
			<td>表明該變量可以接受任何類型。</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>未知類型</td>
			<td>
				<tt>{?}</tt>
			</td>
			<td>表明該變量可以接受任何類型，編譯器不應該檢查其類型。</td>
			<td>&#160;</td>
		</tr>
	</tbody>
</table>

##JavaScript中的類型##

<table>
	<thead valign="bottom">
		<tr>
			<th>類型舉例</th>
			<th>取值舉例</th>
			<th>描述</th>
		</tr>
	</thead>
	<tbody valign="top">
		<tr>
			<td>number</td>
			<td>
				<pre>
1
1.0
-5
1e5
Math.PI
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>Number</td>
			<td>
				<pre>
new Number(true)
</pre>
			</td>
			<td>
				<a href="http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Wrapper_objects_for_primitive_types">Number對像</a>
			</td>
		</tr>
		<tr>
			<td>string</td>
			<td>
				<pre>
&#39;Hello&#39;
&quot;World&quot;
String(42)
</pre>
			</td>
			<td>字符串</td>
		</tr>
		<tr>
			<td>String</td>
			<td>
				<pre>
new String(&#39;Hello&#39;)
new String(42)
</pre>
			</td>
			<td>
				<a href="http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Wrapper_objects_for_primitive_types">String對像</a>
			</td>
		</tr>
		<tr>
			<td>boolean</td>
			<td>
				<pre>
true
false
Boolean(0)
</pre>
			</td>
			<td>Boolean值</td>
		</tr>
		<tr>
			<td>Boolean</td>
			<td>
				<pre>
new Boolean(true)
</pre>
			</td>
			<td>
				<a href="http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Wrapper_objects_for_primitive_types">Boolean對像</a>
			</td>
		</tr>
		<tr>
			<td>RegExp</td>
			<td>
				<pre>
new RegExp(&#39;hello&#39;)
/world/g
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>Date</td>
			<td>
				<pre>
new Date
new Date()
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>null</td>
			<td>
				<pre>
null
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>undefined</td>
			<td>
				<pre>
undefined
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>void</td>
			<td>
				<pre>
function f() {
return;
}
</pre>
			</td>
			<td>沒有返回值</td>
		</tr>
		<tr>
			<td>Array</td>
			<td>
				<pre>
[&#39;foo&#39;, 0.3, null]
[]
</pre>
			</td>
			<td>無類型數組</td>
		</tr>
		<tr>
			<td>Array.&lt;number&gt;</td>
			<td>
				<pre>
[11, 22, 33]
</pre>
			</td>
			<td>數字數組</td>
		</tr>
		<tr>
			<td>Array.&lt;Array.&lt;string&gt;&gt;</td>
			<td>
				<pre>
[[&#39;one&#39;, &#39;two&#39;, &#39;three&#39;], [&#39;foo&#39;, &#39;bar&#39;]]
</pre>
			</td>
			<td>以字符串為元素的數組，作為另一個數組的元素</td>
		</tr>
		<tr>
			<td>Object</td>
			<td>
				<pre>
{}
{foo: &#39;abc&#39;, bar: 123, baz: null}
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>Object.&lt;string&gt;</td>
			<td>
				<pre>
{&#39;foo&#39;: &#39;bar&#39;}
</pre>
			</td>
			<td>值為字符串的對象</td>
		</tr>
		<tr>
			<td>Object.&lt;number, string&gt;</td>
			<td>
				<pre>
var obj = {};
obj[1] = &#39;bar&#39;;
</pre>
			</td>
			<td>鍵為整數，值為字符串的對象。 注意，js當中鍵總是會隱式轉換為字符串。所以 
			<tt>obj[&#39;1&#39;] == obj[1]</tt> 。鍵在for…in…循環中，總是字符串類型。但在對像中索引時編譯器會驗證鍵的類型。</td>
		</tr>
		<tr>
			<td>Function</td>
			<td>
				<pre>
function(x, y) {
return x * y;
}
</pre>
			</td>
			<td>
				<a href="http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#Wrapper_objects_for_primitive_types">Function對像</a>
			</td>
		</tr>
		<tr>
			<td>function(number, number): number</td>
			<td>
				<pre>
function(x, y) {
return x * y;
}
</pre>
			</td>
			<td>函數值</td>
		</tr>
		<tr>
			<td>類</td>
			<td>
				<pre>
/** @constructor */
function SomeClass() {}

new SomeClass();
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>接口</td>
			<td>
				<pre>
/** @interface */
function SomeInterface() {}

SomeInterface.prototype.draw = function() {};
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>project.MyClass</td>
			<td>
				<pre>
/** @constructor */
project.MyClass = function () {}

new project.MyClass()
</pre>
			</td>
			<td>&#160;</td>
		</tr>
		<tr>
			<td>project.MyEnum</td>
			<td>
				<pre>
/** @enum {string} */
project.MyEnum = {
/** The color blue. */
BLUE: &#39;#0000dd&#39;,
/** The color red. */
RED: &#39;#dd0000&#39;
};
</pre>
			</td>
			<td>
				<p>枚舉</p>
				<p>JSDoc中枚舉的值都是可選的.</p>
			</td>
		</tr>
		<tr>
			<td>Element</td>
			<td>
				<pre>
document.createElement(&#39;div&#39;)
</pre>
			</td>
			<td>DOM元素</td>
		</tr>
		<tr>
			<td>Node</td>
			<td>
				<pre>
document.body.firstChild
</pre>
			</td>
			<td>DOM節點</td>
		</tr>
		<tr>
			<td>HTMLInputElement</td>
			<td>
				<pre>
htmlDocument.getElementsByTagName(&#39;input&#39;)[0]
</pre>
			</td>
			<td>指明類型的DOM元素</td>
		</tr>
	</tbody>
</table>


###類型轉換###
在類型檢測不准確的情況下，有可能需要添加類型的註釋，並且把類型轉換的表達式寫在括號裡，括號是必須的。如：

	/** @type {number} */ (x)

###可為空與可選的參數和屬性###
因為Javascript是一個弱類型的語言，明白函數參數、類屬性的可選、可為空和未定義之間的細微差別是非常重要的。

對像類型和引用類型默認可為空。如以下表達式：

	/**
	* 傳入值初始化的類
	* @param {Object} value某個值
	* @constructor
	*/
	function MyClass(value) {
	  /**
	   * Some value.
	   * @type {Object}
	   * @private
	   */
	  this.myValue_ = value;
	}

告訴編譯器 myValue_ 屬性為一對像或null。如果 `myValue_` 永遠都不會為null,就應該如下聲明:

	/**
	* 傳入非null值初始化的類
	* @param {!Object} value某個值
	* @constructor
	*/
	function MyClass(value) {
	  /**
	   * Some value.
	   * @type {!Object}
	   * @private
	   */
	  this.myValue_ = value;
	}

這樣，如果編譯器可以識別出 `MyClass` 初始化傳入值為null，就會發出一個警告。

函數的可選參數在運行時可能會是undefined，所以如果他們是類的屬性，那麼必須聲明：

	/**
	* 傳入可選值初始化的類
	* @param {Object=} opt_value某個值（可選）
	* @constructor
	*/
	function MyClass(opt_value) {
	  /**
	   * Some value.
	   * @type {Object|undefined}
	   * @private
	   */
	  this.myValue_ = opt_value;
	}

這告訴編譯器 `myValue_` 可能是一個對象，或 null ，或 undefined 。

注意:可選參數 `opt_value` 被聲明成 `{Object=}` ，而不是 `{Object|undefined}` 。這是因為可選參數可能是undefined。雖然直接寫undefined也並無害處，但鑑於可閱讀性還是寫成上述的樣子。

最後，屬性的可為空和可選並不矛盾，下面的四種聲明各不相同：

	/**
	* 接受四個參數，兩個可為空，兩個可選
	* @param {!Object} nonNull 必不為null
	* @param {Object} mayBeNull 可為null
	* @param {!Object=} opt_nonNull 可選但必不為null
	* @param {Object=} opt_mayBeNull 可選可為nu​​ll
	*/
	function strangeButTrue(nonNull, mayBeNull, opt_nonNull, opt_mayBeNull) {
	  // ...
	};

###類型定義###
有時類型可以變得複雜。一個函數，它接受一個元素的內容可能看起來像：

	/**
	* @param {string} tagName
	* @param {(string|Element|Text|Array.<Element>|Array.<Text>)} contents
	* @return {!Element}
	*/
	goog.createElement = function(tagName, contents) {
	  ...
	};

你可以定義帶 `@typedef` 標記的常用類型表達式。例如：

	/** @typedef {(string|Element|Text|Array.<Element>|Array.<Text>)} */
	goog.ElementContent;
	/**
	* @param {string} tagName
	* @param {goog.ElementContent} contents
	* @return {!Element}
	*/
	goog.createElement = function(tagName, contents) {
	...
	};

###模板類型###
編譯器對模板類型提供有限支持。它只能從字面上通過 this 參數的類型和 this 參數是否丟失推斷匿名函數的 this 類型。

	/**
	* @param {function(this:T, ...)} fn
	* @param {T} thisObj
	* @param {...*} var_args
	* @template T
	*/
	goog.bind = function(fn, thisObj, var_args) {
	...
	};
	//可能出現屬性丟失警告
	goog.bind(function() { this.someProperty; }, new SomeClass());
	//出現this未定義警告
	goog.bind(function() { this.someProperty; });

##註釋##
使用JSDoc。

我們使用[c++的註釋風格](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#Comments)。所有的文件、類、方法和屬性都應該用合適的[JSDoc](https://code.google.com/p/jsdoc-toolkit/)的[標籤](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JSDoc_Tag_Reference)和[類型](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml#JsTypes)註釋。除了 ​​直觀的方法名稱和參數名稱外，方法的描述、方法的參數以及方法的返回值也要包含進去。

行內註釋應該使用 `//` 的形式。

為了避免出現語句片段，要使用正確的大寫單詞開頭，並使用正確的標點符號作為結束。

###註釋語法###
JSDoc的語法基於[JavaDoc](http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html)，許多編譯工具從JSDoc註釋中獲取信息從而進行代碼驗證和優化，所以這些註釋必須符合語法規則。

	/**
	* A JSDoc comment should begin with a slash and 2 asterisks.
	* Inline tags should be enclosed in braces like {@code this}.
	* @desc Block tags should always start on their own line.
	*/

###JSDoc 縮進###
如果你不得不進行換行，那麼你應該像在代碼裡那樣，使用四個空格進行縮進。

	/**
	* Illustrates line wrapping for long param/return descriptions.
	* @param {string} foo This is a param with a description too long to fit in
	* one line.
	* @return {number} This returns something that has a description too long to
	* fit in one line.
	*/
	project.MyClass.prototype.method = function(foo) {
	  return 5;
	};

不必在 `@fileoverview` 標記中使用縮進。

雖然不建議，但依然可以對描述文字進行排版。

	/**
	* This is NOT the preferred indentation method.
	* @param {string} foo This is a param with a description too long to fit in
	* one line.
	* @return {number} This returns something that has a description too long to
	* fit in one line.
	*/
	project.MyClass.prototype.method = function(foo) {
	  return 5;
	};

###JSDoc中的HTML###
像JavaDoc一樣, JSDoc支持很多的HTML標籤，像 `<code>`，`<pre>`，`<tt>`，`<strong>`，`<ul>`，`<ol>`，`<li>`，`<a>`等。

這就意味著不建議採用純文本的格式。所以，不要在JSDoc裡使用空白符進行格式化。

	/**
	* Computes weight based on three factors:
	* items sent
	* items received
	* last timestamp
	*/

上面的註釋會變成這樣：

	Computes weight based on three factors: items sent items received items received last timestamp

所以，用下面的方式代替：

	/**
	* Computes weight based on three factors:
	* <ul>
	* <li>items sent
	* <li>items received
	* <li>last timestamp
	* </ul>
	*/

[JavaDoc](http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html)風格指南對於如何編寫良好的doc註釋是非常有幫助的。

###頂層/文件層註釋###
[版權聲明](http://google-styleguide.googlecode.com/svn/trunk/copyright.html)和作者信息是可選的。頂層註釋的目的是為了讓不熟悉代碼的讀者了解文件中有什麼。它需要描述文件內容，依賴關係以及兼容性的信息。例如：

	/**
	* @fileoverview Description of file, its uses and information
	* about its dependencies.
	*/

###Class評論###
類必須記錄說明與描述和一個類型的標籤，標識的構造函數。類必須加以描述，若是構造函數則需標註出。

	/**
	* Class making something fun and easy.
	* @param {string} arg1 An argument that makes this more interesting.
	* @param {Array.<number>} arg2 List of numbers to be processed.
	* @constructor
	* @extends {goog.Disposable}
	*/
	project.MyClass = function(arg1, arg2) {
	  // ...
	};
	goog.inherits(project.MyClass, goog.Disposable);

###方法和功能註釋###
參數和返回類型應該被記錄下來。如果方法描述從參數或返回類型的描述中明確可知則可以省略。方法描述應該由一個第三人稱表達的句子開始。

	/**
	* Operates on an instance of MyClass and returns something.
	* @param {project.MyClass} obj Instance of MyClass which leads to a long
	* comment that needs to be wrapped to two lines.
	* @return {boolean} Whether something occured.
	*/
	function PR_someMethod(obj) {
	  // ...
	}

###屬性評論###
	/** @constructor */
	project.MyClass = function() {
	/**
	  * Maximum number of things per pane.
	  * @type {number}
	  */
	  this.someProperty = 4;
	}

###JSDoc標籤參考###
你也許在第三方代碼中看到其他類型JSDoc註釋，這些註釋出現在[JSDoc Toolkit標籤的參考](https://code.google.com/p/jsdoc-toolkit/wiki/TagReference)，但目前在谷歌的代碼中不鼓勵使用。你應該將他們當作“保留”字，他們包括：

*	@augments
*	@argument
*	@borrows
*	@class
*	@constant
*	@constructs
*	@default
*	@event
*	@example
*	@field
*	@function
*	@ignore
*	@inner
*	@link
*	@memberOf
*	@name
*	@namespace
*	@property
*	@public
*	@requires
*	@returns
*	@since
*	@static
*	@version

##為goog.provide提供依賴##
只提供頂級符號。

一個類上定義的所有成員應該放在一個文件中。所以，在一個在相同類中定義的包含多個成員的文件中只應該提供頂級的類（例如枚舉、內部類等）。

要這樣寫：

	goog.provide('namespace.MyClass');

不要這樣寫：

	goog.provide('namespace.MyClass');
	goog.provide('namespace.MyClass.Enum');
	goog.provide('namespace.MyClass.InnerClass');
	goog.provide('namespace.MyClass.TypeDef');
	goog.provide('namespace.MyClass.CONSTANT');
	goog.provide('namespace.MyClass.staticMethod');

命名空間的成員也應該提供：

	goog.provide('foo.bar');
	goog.provide('foo.bar.method');
	goog.provide('foo.bar.CONSTANT');

##編譯##
必需。

對於所有面向客戶的代碼來說，使用JS編輯器是必需的，如使用[Closure Compiler](https://developers.google.com/closure/compiler/?csw=1)。

##技巧和訣竅##
JavaScript幫助信息

True和False布爾表達式下邊的布爾表達式都返回false：

*	null
*	undefined
*	''空字符串
*	數字0

但是要小心，因為以下這些返回true：

*	字符串"0"
*	[]空數組
*	{}空對象

下面這樣寫不好：

	while (x != null) {

你可以寫成這種更短的代碼（只要你不期望x為0、空字符串或者false）：

	while (x) {

如果你想檢查字符串是否為null或空，你可以這樣寫：

	if (y != null && y != '') {

但是以下這樣會更簡練更好：

	if (y) {

注意：還有很多不直觀的關於布爾表達式的例子，這裡是一些：

*	Boolean('0') == true '0' != true
*	0 != null 0 == [] 0 == false
*	Boolean(null) == false null != true null != false
*	Boolean(undefined) == false undefined != true undefined != false
*	Boolean([]) == true [] != true [] == false
*	Boolean({}) == true {} != true {} != false

###條件（三元）操作符（？：）###
以下這種寫法可以三元操作符替換：

	if (val != 0) {
	  return foo();
	} else {
	  return bar();
	}

你可以這樣寫來代替：

	return val ? foo() : bar();

三元操作符在生成HTML代碼時也是很有用的：

	var html = '<input type="checkbox"' +
	    (isChecked ? ' checked' : '') +
	    (isEnabled ? '' : ' disabled') +
	    ' name="foo">';

###&& 和||###
二元布爾操作符是可短路的,，只有在必要時才會計算到最後一項。

"||" 被稱作為'default' 操作符，因為可以這樣：

	/** @param {*=} opt_win */
	function foo(opt_win) {
	  var win;
	  if (opt_win) {
	    win = opt_win;
	  } else {
	    win = window;
	  }
	  // ...
	}

你可以這樣寫：

	/** @param {*=} opt_win */
	function foo(opt_win) {
	  var win = opt_win || window;
	  // ...
	}
	"&&" 也可以用來縮減代碼。例如，以下這種寫法可以被縮減：
	if (node​​) {
	  if (node​​.kids) {
	    if (node​​.kids[index]) {
	      foo(node​​.kids[index]);
	    }
	  }
	}

你可以這樣寫：

	if (node​​ && node.kids && node.kids[index]) {
	  foo(node​​.kids[index]);
	}

或者這樣寫：

	var kid = node && node.kids && node.kids[index];
	  if (kid) {
	    foo(kid);
	}

然而以下這樣寫就有點過頭了：

	node && node.kids && node.kids[index] && foo(node​​.kids[index]);

###遍歷節點列表###
節點列表是通過給節點迭代器加一個過濾器來實現的。這表示獲取他的屬性，如length的時間複雜度為O(n)，通過length來遍歷整個列表需要O(n^2)。

	var paragraphs = document.getElementsByTagName('p');
	for (var i = 0; i < paragraphs.length; i++) {
	  doSomething(paragraphs[i]);
	}

這樣寫更好：

	var paragraphs = document.getElementsByTagName('p');
	for (var i = 0, paragraph; paragraph = paragraphs[i]; i++) {
	  doSomething(paragraph);
	}

這種方法對所有的集合和數組(只要數組不包含被認為是false值的元素) 都適用。

在上面的例子中，你也可以通過firstChild和nextSibling屬性來遍歷子節點。

	var parentNode = document.getElementById('foo');
	for (var child = parentNode.firstChild; child; child = child.nextSibling) {
	  doSomething(child);
	}


-----------------------------------------------------------------

