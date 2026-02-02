[Home](../../) | [Projects](../../projects) | [Notes](../) > <a href="./">Battery Management Systems (BMS)</a> > State of Charge (SOC)

# State of Charge (SOC)



## What is SOC

State of Charge (SoC) represents **how much energy is currently stored in a battery relative to its usable capacity**, typically expressed as a percentage (0-100%). From a system and user perspective, SOC is analogous to a *fuel gauge* in a vehicle. It is the most intuitive indicator of remaining battery energy.

A common conceptual definition is:



$$
SOC (\%) = \frac{\text{Remaining charge}}{\text{Usable battery capacity}} \times 100
$$



However, the key nuance is that usable capacity is not a fixed value. It varies with:

* Battery aging and degradation (capacity fade)
* Temperature
* Charge/discharge rate (C-rate)
* Operating voltage limits defined by safety requirements

Because of this, SOC is **not directly measurable** and must always be *estimated*.



## Why is SOC Important?

SOC is a core variable in almost every Battery Management Systems (BMS) decision. Accurate SOC estimation is critical for:

* User Experience
  * Providing reliable remaining runtime or driving range
  * Preventing unexpected shutdowns
  * Avoiding misleading SOC jumps or drift
* System Control & Safety
  * Enabling or disabling charge/discharge based on safe operation limits
  * Preventing overcharge and overdischarge
  * Coordinating with power electronics (inverters, chargers, DC/DC converters)
* Energy Management & Optimization
  * Load balancing and power limiting
  * Optimal charge scheduling
  * Integration with higher-level systems (EMS, vehicle controllers, grid controllers)

In short, a BMS with poor SOC estimation is fundamentally unreliable, regardless of how good the hardware is.



## Why is SOC Estimation Hard?

Unlike voltage or current, SOC cannot be measured with a sensor. The difficulty comes from:

* Non-linear battery behavior
* Strong dependency on temperature and load
* Hysteresis effects
* Cell-to-cell variation
* Aging-related parameter drift

As a result, all SOC values are model-based or inference-based estimates, not ground truth.



## Common SOC Estimation Methods

### Voltage-Based Estimation (OCV Method)

##### Principle

SOC is estimated using the relationship between Open-Circuit Voltage (OCV) and SOC.

##### How it Works

* Measure battery voltage at rest (no load, sufficient relaxation time)
* Map voltage to SOC using a predefined OV-SOC curve.

##### Advantages

* Simple to implement
* No current sensor required
* Useful for initialization or recalibration

##### Limitations

* Requires long rest periods to be accurate
* Highly temperature-dependent

##### Typical Use Cases

* Initial SOC estimation at startup
* Periodic correction for other algorithms

### Coulomb Counting (Current Integration)

##### Principle

SOC is calculated by integrating current over time. (Charge current: +)



$$
SOC(t) = SOC(t_0) + \frac{1}{C} \times \int I(t)dt
$$



##### Advantages

* High resolution and fast response
* Accurate over short time scales
* Works under dynamic load

##### Limitations

* Accumulates error over time (drift)
* Sensitive to current sensor offset and noise
* Requires a reliable initial SOC

##### Typical Use Cases

* Real-time SOC tracking
* Combined with correction mechanisms (OCV, model-based)







