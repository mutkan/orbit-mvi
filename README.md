# Orbit MVI for Kotlin and Android

[![CI status](https://github.com/babylonhealth/orbit-mvi/workflows/Android%20CI/badge.svg)](https://github.com/babylonhealth/orbit-mvi/actions)
[![Download](https://api.bintray.com/packages/babylonpartners/maven/orbit/images/download.svg)](https://bintray.com/babylonpartners/maven/orbit/_latestVersion)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE.md)

![Logo](images/logo.png)

Orbit is an MVI framework for Kotlin and Android which we use at [Babylon Health](https://www.babylonhealth.com).

![slack logo](images/slack-logo-icon.png) [Join us at the Kotlinlang slack!](https://kotlinlang.slack.com/messages/CPM6UMD2P)

If you do not yet have an account with the Kotlinlang slack workspace, [sign up here](https://slack.kotlinlang.org).

## Why Orbit

Orbit provides the minimum structure possible around your redux implementation
to make it easy to use, yet leave you open to use RxJava's power.

When we created Orbit, we initially looked at other redux libraries out there
but felt they didn't meet our needs. Some didn't handle Android lifecycle, and
others had elaborate structured APIs while some provided custom functionality
that RxJava gives you out of the box.

We drew inspiration from [Managing State with RxJava by Jake Wharton](https://www.reddit.com/r/androiddev/comments/656ter/managing_state_with_rxjava_by_jake_wharton/),
[RxFeedback](https://github.com/NoTests/RxFeedback.kt) and [Mosby MVI](https://github.com/sockeqwe/mosby).

For more details about MVI and our implementation, please read

1. [MVI overview](docs/overview.md).
1. [Creating flows in Orbit](docs/orbits.md).

## Getting started

[![Download](https://api.bintray.com/packages/babylonpartners/maven/orbit/images/download.svg)](https://bintray.com/babylonpartners/maven/orbit/_latestVersion)

Include the following dependencies in your build.gradle.kts file:

```kotlin
implementation("com.babylon.orbit:orbit:<latest-version>")
implementation("com.babylon.orbit:orbit-android:<latest-version>")
```

A real-world redux system might look as follows:

``` kotlin
data class State(val total: Int = 0)

data class AddAction(val number: Int)

sealed class SideEffect {
    data class Toast(val text: String) : SideEffect()
}

class CalculatorViewModel : OrbitViewModel<State, SideEffect> (State(), {

    perform("addition")
        .on<AddAction>()
        .sideEffect { post(SideEffect.Toast("Adding ${event.number}")) }
        .reduce {
            currentState.copy(currentState.total + event.number)
        }

    ...
})
```

And then in your activity / fragment

``` kotlin
// Example of injection using koin, your DI system might differ
private val viewModel by viewModel<CalculatorViewModel>()

override fun onCreate() {
    ...
    addButton.setOnClickListener { viewModel.sendAction(AddAction) }
}

override fun onStart() {
    viewModel.connect(this, ::handleState, ::handleSideEffect)
}

private fun handleState(state: State) {
    ...
}

private fun handleSideEffect(sideEffect: SideEffect) {
    when (sideEffect) {
        is SideEffect.Toast -> toast(sideEffect.text)
    }
}
```

Read more about what makes an [orbit](docs/orbits.md).

## Contributing

Please read [CONTRIBUTING.md](CONTRIBUTING.md)
for details on our code of conduct, and the process for submitting pull
requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions
available, see the [tags on this repository](https://github.com/babylonhealth/orbit-mvi/tags).

## License

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE.md)

This project is licensed under the Apache License, Version 2.0 - see the
[LICENSE.md](LICENSE.md) file for details
