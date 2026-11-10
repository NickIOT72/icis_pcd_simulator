# Requirements

## Technologies
* Backend: NodeJS and Python
* Frontend: Electron
* Database: PostgreSQL

## Screens
* Splash screen
    * label:"splash_screen"
    * triggers: none
    * data: []
    * events:
        * Lifecycle Hooks:
            * onInit:
                * Checking()
                    * Session & Auth Checks
                    * Configuration & Assets Loading
                    * Database & Cache Setup
                    * Initial API Calls
                    * Third-Party SDK Initialization
            * onDestroy:
                * CloseScreen()
                * GoToScreen(screen)
        * Event Handlers:
        * UI Logic:
            * BarLoaderUpdate()
        * ViewModel
    * Design: go to screens/splash_screen.html

## Outcome
* Desktop app to design protection and control single liness

## Workflow
* User click on icon of application
* It will open "Splash screen"
    * Wait for "onInit" event finished. Wait 1 second . set "onDestroy" event
* It will open "Home Screen"
    * 