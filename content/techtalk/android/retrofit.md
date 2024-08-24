+++
date = 2024-07-26T10:21:48-04:00
title = "REST Calls using Retrofit"
categories = ["techtalk","code","android"]
tags = ["josdem","techtalks","programming","technology", "android", "retrofit2"]
description = "Retrofit is a HTTP client for Android and Java, it turns your HTTP API into a Java interface. This time I will show you how to create a basic project using Retrofit."
+++

Retrofit is a HTTP client for Android and Java, it turns your HTTP API into a Java interface. This time I will show you how to create a basic project using [Retrofit](http://square.github.io/retrofit/).

## Setup

Create a new project in android studio with no Activity, default options and set the following dependencies in `build.gradle.kts`

```kotlin
implementation("com.squareup.retrofit2:retrofit:$retrofitVersion")
implementation("com.squareup.retrofit2:converter-gson:$retrofitVersion")
```

Here we are using [Gson](https://github.com/google/gson) converter to transform the JSON responses to the model classes.

In this example we are going to use a [Fruitypedia](https://github.com/josdem/fruitypedia-spring-boot) API to get smoothie categories: https://fruitypedia.josdem.io/categories/en

This is the JSON output

```json
[
  {
    "id": 5,
    "name": "Healing"
  },
  {
    "id": 6,
    "name": "Energy"
  },
  {
    "id": 7,
    "name": "Healthy"
  },
  {
    "id": 8,
    "name": "Boost"
  }
]
```

Let's create a basic model data `Category` class

```kotlin
package com.josdem.retrofit.model

data class Category(
    val id: Int,
    val name: String,
)
```

Now we can create the `FruitypediaService` interface that will embody our HTTP response.

```kotlin
package com.josdem.retrofit.service

import com.josdem.retrofit.model.Beverage
import com.josdem.retrofit.model.Category
import retrofit2.Response
import retrofit2.http.GET
import retrofit2.http.Path

interface FruityService {

    @GET("/categories/{language}")
    suspend fun getCategories(
        @Path("language") language: String,
    ): Response<List<Category>>

}
```

Retrofit generates an implementation of the `FruityService` interface, however we need to create a Retrofit builder using a base URL.

```kotlin
package com.josdem.retrofit.service

import retrofit2.Retrofit
import retrofit2.converter.gson.GsonConverterFactory

object RetrofitHelper {
    private const val BASE_URL = "https://fruitypedia.josdem.io/"

    fun getInstance(): Retrofit {
        return Retrofit.Builder().baseUrl(BASE_URL)
            .addConverterFactory(GsonConverterFactory.create())
            .build()
    }
}
```

Finally we are going to call asynchronously using [Kotlin coroutines](https://developer.android.com/kotlin/coroutines). To make it simple from our `MainActivity`

```kotlin
package com.josdem.retrofit

import android.os.Bundle
import android.util.Log
import androidx.activity.ComponentActivity
import com.josdem.retrofit.service.FruityService
import com.josdem.retrofit.service.RetrofitHelper
import kotlinx.coroutines.MainScope
import kotlinx.coroutines.launch
import java.util.Locale

class MainActivity : ComponentActivity() {

    private val language: String = Locale.getDefault().language

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val fruityService = RetrofitHelper.getInstance().create(FruityService::class.java)

        MainScope().launch {
            val result = fruityService.getCategories(language)
            Log.d("categories: ", result.body().toString())
        }
    }
}
```

That's it when you run the project, you will see the categories in the logcat Android Monitor.

```bash
[Category(id=5, name=Healing), Category(id=6, name=Energy), Category(id=7, name=Healthy), Category(id=8, name=Boost)]
```

Here is the test case:

```kotlin
package com.josdem.retrofit

import com.josdem.retrofit.model.Category
import com.josdem.retrofit.service.FruityService
import com.josdem.retrofit.service.RetrofitHelper
import kotlinx.coroutines.test.runTest
import org.junit.Assert.assertEquals
import org.junit.Assert.assertTrue
import org.junit.Test

import retrofit2.Response

const val LANGUAGE = "en"

class FruityServiceTest {

    private val fruityService: FruityService = RetrofitHelper.getInstance().create(FruityService::class.java)

    private suspend fun getCategories(): Response<List<Category>> {
        return fruityService.getCategories(LANGUAGE)
    }

    @Test
    fun shouldGetCategories() =
        runTest {
            val response = getCategories()
            val categories: List<Category>? = response.body()
            assertTrue(response.isSuccessful)
            assertEquals(4, categories?.size)
        }
}
```

Here is the complete API just in case you want to take a look: https://fruitypedia.josdem.io/swagger-ui.html

To browse the code go [here](https://github.com/josdem/android-retrofit-workshop), to download the code:

```bash
git clone git@github.com:josdem/android-retrofit-workshop.git
```

[Return to the main article](/techtalk/android)
