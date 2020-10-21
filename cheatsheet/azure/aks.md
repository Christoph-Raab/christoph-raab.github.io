## Idle Timeout
InBound LoadBalancer hat als Standard (zumindest wenn von Istio erzeugt) eine default IdleTimeout von 4 Minuten (=Minimum).

Standard OutBound LoadBalancer (von kubernetes erzeugt) hingehen hat ein default IdleTimeout von 30 Minuten (= Maximum).