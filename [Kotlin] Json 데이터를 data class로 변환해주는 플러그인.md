* 퇴행성 관절염이니 손가락을 보호하자!
* 그래서 알아본 data class 생성 플러그인
  * [JsonToKotlinClass](https://github.com/wuseal/JsonToKotlinClass)
  * 설치는 IDE의 Settings -> Plugins -> Json to Kotlin 검색하면 나옴.
</br>

* 아래와 같은 Json 데이터가 있다고 했을 때
```json
{
    "glossary":{
        "title":"example glossary",
        "GlossDiv":{
            "title":"S",
            "GlossList":{
                "GlossEntry":{
                    "ID":"SGML",
                    "SortAs":"SGML",
                    "GlossTerm":"Standard Generalized Markup Language",
                    "Acronym":"SGML",
                    "Abbrev":"ISO 8879:1986",
                    "GlossDef":{
                        "para":"A meta-markup language, used to create markup languages such as DocBook.",
                        "GlossSeeAlso":[
                            "GML",
                            "XML"
                        ]
                    },
                    "GlossSee":"markup"
                }
            }
        }
    }
}
```
</br>

* IDE에 설치된 플러그인을 통해 아래와 같이 data class를 자동 생성 할 수 있다.
```kotlin
data class Example(
    val glossary: Glossary
)

data class Glossary(
    val GlossDiv: GlossDiv,
    val title: String
)

data class GlossDiv(
    val GlossList: GlossList,
    val title: String
)

data class GlossList(
    val GlossEntry: GlossEntry
)

data class GlossEntry(
    val Abbrev: String,
    val Acronym: String,
    val GlossDef: GlossDef,
    val GlossSee: String,
    val GlossTerm: String,
    val ID: String,
    val SortAs: String
)

data class GlossDef(
    val GlossSeeAlso: List<String>,
    val para: String
)
```
</br>

* 아래와 같은 유용한 옵션도 제공

![Alt text](/images/json_to_kotlin_property.png)
![Alt text](/images/json_to_kotlin_annotation.png)
![Alt text](/images/json_to_kotlin_other.png)
![Alt text](/images/json_to_kotlin_extensions.png)
