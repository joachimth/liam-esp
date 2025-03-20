graph TD;
    A[setup()] -->|Initierer hardware| B[scan_I2C()]
    A --> C[check_SPI()]
    A --> D[start subsystems]
    D -->|Accelerometer| E[io_accelerometer.start()]
    D -->|Dockingstation| F[dockingstation.start()]
    D -->|GPS| G[gps.start()]
    D -->|Batteri| H[battery.start()]
    D -->|Klippetidsplan| I[mowingSchedule.start()]
    
    J[loop()] -->|Tjek Factory Reset| K[Configuration::wipe()]
    J -->|Tjek Emergency Stop| L[stateController.setState(STOP)]
    J -->|Opdater sonar| M[sonar.process()]
    J -->|Håndter tilstande| N[stateController.getStateInstance()->process()]
    J -->|Håndter hjul| O[wheelController.process()]
    J -->|Håndter cutter| P[cutter.process()]
    
    Q[stateController.setState()] -->|Skift tilstand| R[Definitions::MOWER_STATES]
    R -->|DOCKED| S[Docked]
    R -->|MOWING| T[Mowing]
    R -->|DOCKING| U[Docking]
    R -->|CHARGING| V[Charging]
    
    S -->|Stopper cutter| dockingstation.stop()
    T -->|Starter cutter| cutter.start()
    T -->|Tjek batteri| stateController.setState(DOCKING)
    U -->|Stopper cutter| cutter.stop()
    U -->|Kører til docking| wheelController.forward()
    V -->|Tjek opladning| battery.isFullyCharged()