# 함수형 코틀린 서적에 있는 DSL 샘플
  * **향후 DSL 개발 시 참고하자**
  * **DSL 완성 코드**
      ```kotlin
      val commuter = bicycle {
          description("Fast carbon commuter")

          bar {
              barType = BarType.FLAT
              material = Material.ALUMINIUM
          }

          frame {
              material = Material.CARBON
              backWheel {
                  material = Material.ALUMINIUM
                  brake = Brake.DISK
              }
          }

          fork {
              material = Material.CARBON
              frontWheel {
                  material = Material.ALUMINIUM
                  brake = Brake.DISK
              }
          }
      }    
      ```
    
  * **DSL 코드 구조**  
      ```kotlin
      interface Element {
          fun render(builder: StringBuilder, indent: String)
      }

      @DslMarker
      annotation class ElementMarker

      enum class Material {
          CARBON, STEEL, TITANIUM, ALUMINIUM
      }

      enum class BarType {
          DROP, FLAT, TT, BULLHORN
      }

      enum class Brake {
          RIM, DISK
      }

      @ElementMarker
      abstract class Part(private val name: String) : Element {
          private val children = arrayListOf<Element>()
          protected val attributes = hashMapOf<String, String>()
          protected fun <T : Element> initElement(element: T, init: T.() -> Unit): T {
              element.init()
              children.add(element)
              return element
          }

          override fun render(builder: StringBuilder, indent: String) {
              builder.append("$indent<$name${renderAttributes()}>\n")
              children.forEach { c -> c.render(builder, indent + "\t") }
              builder.append("$indent<$name>\n")
          }

          private fun renderAttributes(): String = buildString {
              attributes.forEach { attr, value -> append(" $attr=\"$value\"") }
          }

          override fun toString(): String = buildString {
              render(this, "")
          }
      }

      abstract class PartWithMaterial(name: String) : Part(name) {
          var material: Material
              get() = Material.valueOf(attributes["material"]!!)
              set(value) {
                  attributes["material"] = value.name
              }
      }

      class Frame : PartWithMaterial("frame") {
          fun backWheel(init: Wheel.() -> Unit) = initElement(Wheel(), init)
      }

      class Wheel : PartWithMaterial("wheel") {
          var brake: Brake
              get() = Brake.valueOf(attributes["brake"]!!)
              set(value) {
                  attributes["brake"] = value.name
              }
      }

      class Bar : PartWithMaterial("bar") {
          var barType: BarType
              get() = BarType.valueOf(attributes["type"]!!)
              set(value) {
                  attributes["type"] = value.name
              }
      }

      class Fork : PartWithMaterial("fork") {
          fun frontWheel(init: Wheel.() -> Unit) = initElement(Wheel(), init)
      }

      class Bicycle : Part("bicycle") {

          fun description(description: String) {
              attributes["description"] = description
          }

          fun frame(init: Frame.() -> Unit) = initElement(Frame(), init)

          fun fork(init: Fork.() -> Unit) = initElement(Fork(), init)

          fun bar(init: Bar.() -> Unit) = initElement(Bar(), init)
      }    
      ```
    
  * **DSL 용 입력 함수**
      ```kotlin
      fun bicycle(init: Bicycle.() -> Unit): Bicycle {
          val cycle = Bicycle()
          cycle.init()
          return cycle
      }    
      ```
