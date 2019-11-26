# MyCodeStyles v1
👶 Разрабатываю свой code style

Настало время подытожить свои **пока небольшие** знание по языкам, и разработать свой собственный стиль. В основу стиля легли найденые мной best practices, буду включать их со ссылками на первоисточники. Сразу оговорим, ___написанные до этой статьи проекты, скорее всего я рефакторить под стайлгайд не буду, к тому же этот стиль всё время находится в разработке___. В общем погнали.

> Описание code style под конкретный язык будет на [странице wiki](https://github.com/EvgenySamarin/MyCodeStyles/wiki/Kotlin), на соответствующей языку закладке. Здесь будет общее описание для android SDK в целом.

Как минимум перед прочтением следует ознакомиться с отличным гайдом, который был мной бережно и с трепетом форкнут в свои кулуары:
[Ribots guidline](https://github.com/EvgenySamarin/android-guidelines/blob/master/project_and_code_guidelines.md)

## Own Android UI component naming convention

Так как разработка у меня идет под андроид, примем определённое соглашение об именовании элементов UI элементов. Для начала все переменные используемые в приложении могут использовать сокращение базовых элементов, с таким расчетом чтоб читаемость элемента была минимально искажена. Наименования всех элементов в той или иной степени должны подчиняться паттерну `<WHAT>_<WHERE>_<DESCRIPTION>_<SIZE>` 

Принятые сокращения: 

Компонент    | Сокращение |     | Компонент         | Сокращение |     | Компонент               | Сокращение    | 
---          | ---        | --- | ---               | ---        | --- | ---                     | ---           | 
Activity     | act        |     | Icon              | ic         |     | NavigationView          | nav_view      | 
Fragment     | frag       |     | MaterialCardView  | card       |     | TabLayout     	         | tabs          | 
ImageView    | img        |     | Notification      | notify     |     | TabItem                 | tab           | 
TextView     | txt        |     | RecyclerView      | recycler   |     | navigation              | nav           | 
LinearLayout | lin        |     | MaterialButton    | btn        |     | EditText                | edt           |
CheckBox     | chk        |     | Spinner           | spn        |     | BottomNavigationView    | btm_nav_view  |
Chip         | chp        |     | ChipGroup         | chp_grp    |     | CollapsingToolbarLayout | cllps_toolbar |
AppBarLayout | app_bar    |     | CoordinatorLayout | coord      |     | FloatingActionButton    | fab           |
WebView      | web        |     | ViewPager         | pager      |     | TextInputLayout         | txt_in_lay    |
ScrollView   | scroll     |     | NestedScrollView  | nscroll    |     | TextInputEditText       | txt_in        |
ProgressBar  | prg_bar    |     | RadioGroup        | radio_grp  |     | RadioButton             | radio         |
GreedView    | grd        |     | Switch            | swch       |     |||
 ||     |||     |||
 ||     |||     |||


### Именование Layout Files

 > ⚠ В названии файла разметки **`нельзя`** использовать сокращения слов. 
 
Названия файлов ресурсов пишутся латиницой, строчными буквами с использованием snake_case. 
 Правая часть добавляется в случае использования нескольких компонентов одного типа

 Компонент         | ClassName    | Переменнные                    | LayoutName  
 ---               | ---          | ---                            | --- 
  Activity         | TestActivity | ` val testAct: TestActivity `  | activity_test.xml
  Fragment         | TestFragment | ` val testFrag: TestFragment ` | fragment_test.xml
  AdapterView item | ---          | ` val itemTest               ` | item_test.xml
  
  ### Именование элементов разметки XML
  Атрибуты для компонентов разметки пишутся в столбец, по 1 атрибуту в строке.
  Следует придерживаться следующего порядка добавления атрибутов для компонентов.
  1. Переопределённый стиль
  2. Идентификатор
  3. Атрибуты размера
  4. Прочие атрибуты
  
  > ⚠ Идентификаторы View элементов пишутся латиницой, строчными буквами с использованием snake_case и принятых выше сокращений.
  Пример:
 ```xml
<TextView
    android:id="@+id/txt_fragment_desctiption"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="@string/STUB_TXT_USER_NAME_WORD"
    android:textAppearance="@style/Body.Body1" />
```
  
## Использование proguard
Правила proguard для сторонних библиотек должны находится в директории `Project\<название модуля>\proguard\`, для каждой библиотеки создается отдельный файл правил. 
Шаблоны файлов берутся из [этого репозитория](https://github.com/krschultz/android-proguard-snippets/tree/master/libraries).

Все остальные правила - для proguard должны быть сформированы в коде с помощью аннотации `@keep`. 
Также в модуль следует включить основное правило:
```proguard
##
# Описываем набор правил для обфускации кода:
# Правила для сторонних библиотек, находятся в директории app/proguard в виде отдельных файлов.
# Шаблоны файлов берутся из репозитория:
# https://github.com/krschultz/android-proguard-snippets/tree/master/libraries
#
# For more details, see
#   http://developer.android.com/guide/developing/tools/proguard.html
#
# author EvgenySamarin [github](https://github.com/EvgenySamarin)
# since 20191018 v2
##

##---------------------------##
## Общие правила, для всех проектов
-printusage build/outputs/mapping/release/usage.txt # Печатаем вырезанные библиотеки и методы
-printmapping build/outputs/mapping/release/mapping.txt # Печатаем словарь ассоциаций
-printseeds build/outputs/mapping/release/seeds.txt # Печатаем все точки входа в библиотеки

-dontobfuscate # отключаем обфускацию на время отладки, закоментируй после проверки
##===========================##
```
## Gradle rules
Приложение по возможности должно быть разбито на модули. В главном build.gradle скрипте (скрипт проекта) описываются подключаемые библиотеки, а также их версии. Версии выносятся в отдельные переменные и анотируются встроенными комментариями. Названия переменных пишутся в snake_case. Kapt библиотеки должны быть отделены от implementation библиотек (описаны в разных массивах).

Пример project build gradle.
```gradle
ext {
    okhttp3_version = "4.1.0" //для создания клиента Http
    kotlin_coroutines_version = "1.3.2" //для работы с асинхронными методами
    material_version = "1.0.0" //для красоты в UI
    architecture_components_version = "2.1.0" //для изучения современного подхода к разработке

    libraries = [
            //kotlin coroutines
            "org.jetbrains.kotlinx:kotlinx-coroutines-android:$kotlin_coroutines_version",
            "org.jetbrains.kotlinx:kotlinx-coroutines-core:$kotlin_coroutines_version",
            "com.jakewharton.retrofit:retrofit2-kotlin-coroutines-adapter:$retrofit2_coroutines_version",

            //ViewModel and liveData
            "androidx.lifecycle:lifecycle-extensions:$architecture_components_version",
            "androidx.lifecycle:lifecycle-viewmodel-ktx:$architecture_components_version",

            //Dagger2
            "com.google.dagger:dagger:$daggerVersion",
            "com.google.dagger:dagger-android:$daggerVersion",
            "com.google.dagger:dagger-android-support:$daggerVersion"
    ]
}

buildscript {
    ext {
        kotlin_version = '1.3.60'
        dexcount_version = '0.8.6'
        gradle_version = '3.5.2'
        navigation_safe_args_version = "1.0.0"
    }

    repositories {
        jcenter()
        google() //репозиторий подключается в последнюю очередь
    }
    dependencies {
        classpath "com.android.tools.build:gradle:$gradle_version"
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        classpath "com.getkeepsafe.dexcount:dexcount-gradle-plugin:$dexcount_version"
        classpath "android.arch.navigation:navigation-safe-args-gradle-plugin:$navigation_safe_args_version"
    }
}

allprojects {
    repositories {
        jcenter()
        google()
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```

