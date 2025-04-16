|                                             | Deploy URL | Base HREF |
| ------------------------------------------- | :--------: | :-------: |
| Initial scripts (index.html)                |   ✅ 👍    |   ✅ 👍   |
| Initial stylesheets (index.html)            |   ✅ 👍    |   ✅ 👍   |
| Lazy scripts (routes/import()/etc.)         |   ✅ 👍    |   ✅ 👍   |
| Processed CSS resources (images/fonts/etc.) |   ✅ 👍    |   ✅ 👍   |
| Relative template (HTML) assets             |   ❌ 👎    |   ✅ 👍   |
| Angular Router Default Base (APP_BASE_HREF) |     ❌     |  ✅ \*1   |
| Single reference in deployed Application    |   ❌ 👎    |   ✅ 👍   |
| Special resource logic within CLI           |   ✅ 👎    |   ❌ 👍   |
| Relative fetch/XMLHttpRequest               |     ❌     |    ✅     |

✅ - has/affects the item/trait
❌ - NOT have/affect the item/trait
👍 - favorable behavior
👎 - unfavorable behavior

* 👀if you have MORE complicated setups -> MANUALLY configure the `APP_BASE_HREF` token | application👀
  * _Example:_
    * application routing base is `/`
    * assets/scripts/etc. are | `/assets/`
