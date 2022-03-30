# タイトル
TypeScript課題②についての質問

<br>
<br>

# 実行環境
## package.json内容
```json
{
  "name": "typescript-study",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "@typescript-eslint/eslint-plugin": "^5.16.0",
    "@typescript-eslint/parser": "^5.16.0",
    "eslint": "^7.32.0",
    "eslint-config-prettier": "^8.5.0",
    "eslint-config-standard": "^16.0.3",
    "eslint-plugin-import": "^2.25.4",
    "eslint-plugin-node": "^11.1.0",
    "eslint-plugin-promise": "^5.2.0",
    "prettier": "^2.6.1",
    "typescript": "^4.6.3"
  }
}
```

<br>

## tsconfig.json
trueのみ
```json
{
  "compilerOptions": {
    "target": "es2017",
    "module": "commonjs",
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "skipLibCheck": true
  }
}
```

<br>
<br>

# 該当のソースコード
```typescript
type MyObj = {
  a: string;
  b: string;
  bb?: string;
  bbb?: string;
};
type MyObjKey = keyof MyObj;
type MyObjVal = MyObj[keyof MyObj];

class ObjectWrapper {
  private _obj;

  /***
   * 引数のオブジェクトのコピーを this._objに設定
   */
  constructor(_obj: MyObj) {
    this._obj = { ..._obj };
  }

  /**
   * this._objのコピーを返却
   * @return Object
   */
  get obj() {
    return { ...this._obj };
  }

  /**
   * this._obj[key] に valを設定。keyがthis._objに存在しない場合、falseを返却
   * @param key オブジェクトのキー
   * @param val オブジェクトの値
   */
  set(key: MyObjKey, val: MyObjVal): boolean {
    this._obj[key] = val;
    return true;
  }

  /**
   * 指定したキーの値のコピーを返却
   * 指定のキーが存在しない場合 undefinedを返却
   * @param key オブジェクトのキー
   */
  get(key: MyObjKey): MyObjVal {
    const result = this._obj[key];
    return result;
  }

  /**
   * 指定した値を持つkeyの配列を返却。該当のものがなければ空の配列を返却。
   */
  findKeys(val: MyObjVal): MyObjKey[] {
    /* Object.entries()メソッドを使えるようにするために、tsconfig.jsonのtargetを「"es2017"」にしています */
    const result: MyObjKey[] = [];
    for (let [key, value] of Object.entries(this._obj)) {
      if (value === val) {
        result.push(key);
      }
    }
    return result;
  }
}

/**
 * check script
 * 完成したら、以下のスクリプトがすべてOKになる。
 */
const obj1 = { a: '01', b: '02' };
const wrappedObj1 = new ObjectWrapper(obj1);

if (wrappedObj1.obj.a === '01') {
  console.log('OK: get obj()');
} else {
  console.error('NG: get obj()');
}

if (/* wrappedObj1.set('c', '03') === false && */ wrappedObj1.set('b', '04') === true && wrappedObj1.obj.b === '04') {
  console.log('OK: set(key, val)');
} else {
  console.error('NG: set(key, val)');
}

if (wrappedObj1.get('b') === '04' /* && wrappedObj1.get('c') === undefined */) {
  console.log('OK: get(key)');
} else {
  console.error('NG: get(key)');
}

const obj2 = { a: '01', b: '02', bb: '02', bbb: '02' };
const wrappedObj2 = new ObjectWrapper(obj2);
const keys = wrappedObj2.findKeys('02');
if (wrappedObj2.findKeys('03').length === 0 && keys.includes('b') && keys.includes('bb') && keys.includes('bbb') && keys.length === 3) {
  console.log('OK: findKeys(val)');
} else {
  console.error('NG: findKeys(val)');
}
```

<br>
<br>

# 発生している問題
## 1.setメソッドで以下のようなエラーが発生している
  > 型 'MyObjVal' を型 'string' に割り当てることはできません。
    型 'undefined' を型 'string' に割り当てることはできません。

```typescript
////setメソッドのみ
set(key: MyObjKey, val: MyObjVal): boolean {
    this._obj[key] = val; //エラーが発生している部分
    return true;
  }
```

<br>

## 2.findkeyメソッドで以下のようなエラーが発生している
> 型 'string' の引数を型 'keyof MyObj' のパラメーターに割り当てることはできません。

```typescript
////findKeysメソッドのみ
findKeys(val: MyObjVal): MyObjKey[] {
    const result: MyObjKey[] = [];
    for (let [key, value] of Object.entries(this._obj)) {
      if (value === val) {
        result.push(key); //エラーが発生している部分
      }
    }
    return result;
  }
```

<br>
<br>

# 試した/調べたこと
## 1. setメソッド
* MyObjを以下のように変更した
  * エラーは解消されましたが、型安全の観点から見るとあまり良くないように感じています。
```typescript
type MyObj = {
  a: string | undefined,;
  b: string | undefined,;
  bb?: string | undefined,;
  bbb?: string | undefined,;
};
```
* オプショナルプロパティを使用した型エイリアスを用いて```type M = P [keyof P]```を使った際に、Union型の型定義の中に```undefined```が入らないようにする方法があるかどうか
  * 見つかりませんでした

<br>

## 2.findkeyメソッド
* ```for...of```内で型注釈する方法
  * できませんでした