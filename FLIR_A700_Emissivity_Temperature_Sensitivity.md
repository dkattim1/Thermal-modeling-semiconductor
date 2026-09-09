# FLIR A700 Infrared Thermography, Emissivity, and Temperature Sensitivity

## 1. Purpose of this note

This document consolidates the complete discussion on:

- how an infrared thermal camera measures temperature,
- how emissivity enters the measurement,
- how uncertainty in emissivity changes reported temperature,
- how the FLIR A700 uses emissivity,
- the difference between Stefan–Boltzmann sensitivity and FLIR band-limited radiometric sensitivity,
- derivation of the local temperature sensitivity to emissivity,
- the specific case of a hot wafer in a cylindrical silicon-processing chamber,
- and the numerical example for:
  - wafer temperature: \(450^\circ\mathrm{C}\),
  - surroundings / reflected apparent temperature: \(60^\circ\mathrm{C}\),
  - true surface emissivity: \(\varepsilon=0.85\).

The goal is to make this note self-contained and usable as a reference.

---

# 2. How an IR thermal camera works

An infrared thermal camera detects infrared radiation emitted by objects because of their temperature.

The basic chain is:

\[
\boxed{
\text{Object temperature}
\rightarrow
\text{infrared radiation}
\rightarrow
\text{camera sensor}
\rightarrow
\text{electrical signal}
\rightarrow
\text{temperature estimate}
\rightarrow
\text{thermal image}
}
\]

Every object above absolute zero emits electromagnetic radiation.

For objects near room temperature and moderately elevated temperatures, much of the useful thermal radiation lies in the long-wave infrared region.

Many thermal cameras, including the FLIR A700 family, work approximately in the:

\[
\boxed{7.5-14\ \mu\mathrm{m}}
\]

spectral range.

A thermal camera typically works as follows:

1. The object emits infrared radiation.
2. An IR-transparent lens focuses that radiation.
3. The detector absorbs the radiation.
4. In an uncooled microbolometer camera, individual pixels warm slightly.
5. Their electrical resistance changes.
6. Electronics measure the response.
7. The camera applies radiometric calibration and corrections.
8. The camera estimates temperature.
9. Software maps temperatures or radiances into a false-color image.

The visible colors in a thermal image are not actual infrared colors. They are a visualization of temperature or radiance.

A very important principle is:

\[
\boxed{\text{The thermal camera does not directly measure temperature.}}
\]

It measures radiation and then infers temperature.

---

# 3. Planck radiation law

The spectral radiance of a blackbody is described by Planck's law:

\[
B_\lambda(T)=
\frac{2hc^2}{\lambda^5}
\frac{1}{
\exp\left(\frac{hc}{\lambda kT}\right)-1
}
\]

where:

- \(B_\lambda\) = blackbody spectral radiance,
- \(h\) = Planck constant,
- \(c\) = speed of light,
- \(k\) = Boltzmann constant,
- \(\lambda\) = wavelength,
- \(T\) = absolute temperature in kelvin.

A camera with a finite spectral band responds only to part of the full blackbody spectrum.

For an idealized FLIR A700 spectral band, define:

\[
\boxed{
L_b(T)
=
\int_{7.5\mu m}^{14\mu m}
B_\lambda(T)\,d\lambda
}
\]

This quantity represents the blackbody radiance over the camera's approximate spectral band.

---

# 4. Emissivity

Emissivity is the ratio of radiation emitted by a real surface to that emitted by an ideal blackbody at the same temperature.

\[
0\le \varepsilon \le 1
\]

A blackbody has:

\[
\varepsilon=1
\]

A high-emissivity surface may have:

\[
\varepsilon \approx 0.9-0.98
\]

A polished metal may have much lower emissivity.

For an opaque surface:

\[
\tau \approx 0
\]

and from energy conservation,

\[
\alpha+\rho=1
\]

For a surface in thermal equilibrium, Kirchhoff's law gives:

\[
\alpha \approx \varepsilon
\]

so approximately:

\[
\boxed{
\rho \approx 1-\varepsilon
}
\]

Therefore a low-emissivity surface is usually highly reflective in the same spectral region.

This is why shiny metals can behave like thermal mirrors.

---

# 5. Radiation seen by an IR camera

For an opaque surface, ignoring atmosphere and windows for the moment, the camera sees approximately:

\[
\boxed{
L_{\mathrm{meas}}
=
\varepsilon L(T_s)
+
(1-\varepsilon)L(T_{\mathrm{refl}})
}
\]

where:

- \(L_{\mathrm{meas}}\) = radiance measured by the camera,
- \(T_s\) = actual surface temperature,
- \(T_{\mathrm{refl}}\) = reflected apparent temperature of the surroundings,
- \(\varepsilon\) = emissivity.

So the camera sees both:

\[
\boxed{
\text{emission from the object}
+
\text{reflection of the surroundings}
}
\]

If:

\[
\varepsilon=0.9
\]

then the surface signal is approximately:

- 90% associated with the object's own emission,
- 10% associated with reflected surroundings.

If:

\[
\varepsilon=0.2
\]

then reflected radiation becomes dominant.

---

# 6. Why low-emissivity surfaces are difficult

For a hot high-emissivity surface, most radiation seen by the camera originates from the object.

For a shiny low-emissivity metal, a large fraction may be reflected radiation from:

- chamber walls,
- hot heaters,
- nearby hardware,
- the operator,
- the ceiling,
- cold windows,
- other thermal sources.

Therefore simply changing the emissivity setting cannot always fully correct a poor low-emissivity measurement.

---

# 7. Simplified Stefan–Boltzmann radiation model

For total hemispherical radiation, the net radiative heat flux between a surface and large surroundings can be written approximately as:

\[
q''_{\mathrm{rad}}
=
\varepsilon\sigma
\left(
T_s^4-T_{\mathrm{sur}}^4
\right)
\]

where:

- \(\sigma\) = Stefan–Boltzmann constant,
- \(T_s\) = surface temperature,
- \(T_{\mathrm{sur}}\) = surroundings temperature.

For thermographic interpretation, a simplified radiance relation may similarly be written in proportional form as:

\[
L(T)\propto T^4
\]

This total-spectrum model is useful for heat-transfer calculations.

However, a FLIR A700 does not measure all wavelengths.

That distinction becomes very important later.

---

# 8. How emissivity error changes inferred temperature in the Stefan–Boltzmann approximation

Suppose the true emissivity is:

\[
\varepsilon_t
\]

but the emissivity entered into the camera or calculation is:

\[
\varepsilon_a
\]

Using a total-spectrum \(T^4\) approximation:

\[
\boxed{
T_{\mathrm{camera}}^4
=
T_{\mathrm{refl}}^4
+
\frac{\varepsilon_t}{\varepsilon_a}
\left(
T_s^4-T_{\mathrm{refl}}^4
\right)
}
\]

or:

\[
\boxed{
T_{\mathrm{camera}}
=
\left[
T_{\mathrm{refl}}^4
+
\frac{\varepsilon_t}{\varepsilon_a}
\left(
T_s^4-T_{\mathrm{refl}}^4
\right)
\right]^{1/4}
}
\]

For a hot object:

\[
\varepsilon_{\mathrm{assumed}}
>
\varepsilon_{\mathrm{true}}
\]

generally gives:

\[
\boxed{
T_{\mathrm{reported}}
<
T_{\mathrm{true}}
}
\]

and:

\[
\varepsilon_{\mathrm{assumed}}
<
\varepsilon_{\mathrm{true}}
\]

generally gives:

\[
\boxed{
T_{\mathrm{reported}}
>
T_{\mathrm{true}}
}
\]

---

# 9. Stefan–Boltzmann sensitivity to emissivity

Differentiate the total-spectrum relationship around the correct operating point.

The sensitivity is:

\[
\boxed{
\frac{\partial T}{\partial \varepsilon}
=
-
\frac{
T_s^4-T_{\mathrm{refl}}^4
}{
4\varepsilon T_s^3
}
}
\]

This can also be written as:

\[
\boxed{
\frac{\partial T}{\partial \varepsilon}
=
-\frac{T_s}{4\varepsilon}
\left[
1-
\left(
\frac{T_{\mathrm{refl}}}{T_s}
\right)^4
\right]
}
\]

If:

\[
T_s \gg T_{\mathrm{refl}}
\]

then:

\[
\left(
\frac{T_{\mathrm{refl}}}{T_s}
\right)^4
\ll 1
\]

and therefore:

\[
\boxed{
\frac{\partial T}{\partial \varepsilon}
\approx
-\frac{T}{4\varepsilon}
}
\]

This is the origin of the familiar \(1/4\) factor.

---

# 10. Derivation of \(\frac{dT}{d\varepsilon}=-\frac{T}{4\varepsilon}\)

Start with the simplified case:

\[
q=\varepsilon\sigma T^4
\]

Assume the measured radiation \(q\) is held fixed.

Then:

\[
T=
\left(
\frac{q}{\varepsilon\sigma}
\right)^{1/4}
\]

Differentiate with respect to emissivity:

\[
\frac{dT}{d\varepsilon}
=
-\frac{1}{4}
\left(
\frac{q}{\sigma}
\right)^{1/4}
\varepsilon^{-5/4}
\]

Since:

\[
T=
\left(
\frac{q}{\sigma}
\right)^{1/4}
\varepsilon^{-1/4}
\]

we obtain:

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-\frac{T}{4\varepsilon}
}
\]

The sign is negative.

So increasing the assumed emissivity for the same measured radiation decreases the inferred temperature.

---

# 11. Why the \(1/4\) formula is not the correct FLIR A700 camera sensitivity

The formula:

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-\frac{T}{4\varepsilon}
}
\]

comes from:

\[
L(T)\propto T^4
\]

which assumes total-spectrum thermal radiation.

The FLIR A700 only measures approximately:

\[
\boxed{7.5-14\ \mu\mathrm{m}}
\]

Therefore its detected radiance is not proportional to \(T^4\).

Instead:

\[
\boxed{
L_b(T)
=
\int_{7.5\mu m}^{14\mu m}
B_\lambda(T)\,d\lambda
}
\]

and:

\[
L_b(T)\neq CT^4
\]

over the finite camera spectral band.

This changes the relationship between radiance and inferred temperature.

---

# 12. FLIR A700 radiometric measurement equation

The A700-family documentation describes radiometric corrections using emissivity together with:

- reflected apparent temperature,
- atmospheric temperature,
- atmospheric transmission,
- distance,
- humidity,
- external optics / window transmission,
- external optics temperature.

A generic FLIR-style measurement equation is:

\[
\boxed{
U_{\mathrm{tot}}
=
\varepsilon\tau U_{\mathrm{obj}}
+
(1-\varepsilon)\tau U_{\mathrm{refl}}
+
(1-\tau)U_{\mathrm{atm}}
}
\]

where:

- \(U_{\mathrm{tot}}\) = measured detector signal,
- \(U_{\mathrm{obj}}\) = signal corresponding to blackbody object temperature,
- \(U_{\mathrm{refl}}\) = signal corresponding to reflected apparent temperature,
- \(U_{\mathrm{atm}}\) = atmospheric emission term,
- \(\varepsilon\) = emissivity,
- \(\tau\) = atmospheric transmission.

Rearranging:

\[
\boxed{
U_{\mathrm{obj}}
=
\frac{U_{\mathrm{tot}}}{\varepsilon\tau}
-
\frac{1-\varepsilon}{\varepsilon}U_{\mathrm{refl}}
-
\frac{1-\tau}{\varepsilon\tau}U_{\mathrm{atm}}
}
\]

The camera then converts:

\[
U_{\mathrm{obj}}
\rightarrow
T_{\mathrm{obj}}
\]

using its factory radiometric calibration.

Thus the camera processing is conceptually:

\[
\boxed{
\text{microbolometer signal}
\rightarrow
U_{\mathrm{tot}}
\rightarrow
\text{radiometric corrections}
\rightarrow
U_{\mathrm{obj}}
\rightarrow
T_{\mathrm{obj}}
}
\]

---

# 13. Emissivity does two things in the camera model

Emissivity is not merely used as a multiplicative correction.

For an opaque surface:

\[
\rho\approx1-\varepsilon
\]

So entering:

\[
\varepsilon=0.90
\]

means the model approximately treats the surface as:

- 90% object emission,
- 10% reflected surroundings.

Entering:

\[
\varepsilon=0.30
\]

means approximately:

- 30% object emission,
- 70% reflected surroundings.

This is why reflected apparent temperature becomes especially important when emissivity is low.

---

# 14. Why changing emissivity changes the reported FLIR temperature

Suppose the detector measures a fixed radiance:

\[
L_{\mathrm{meas}}
\]

Changing the emissivity setting does not change the detector signal.

It changes the camera's interpretation of that signal.

If the camera assumes:

\[
\varepsilon=0.95
\]

it attributes most of the signal to the object.

If the camera assumes:

\[
\varepsilon=0.50
\]

it attributes much more of the signal to reflected surroundings.

The camera subtracts the reflected contribution and then calculates the object radiance needed to produce the measured signal.

Thus:

\[
\boxed{
\text{Measured radiation fixed}
\rightarrow
\text{change assumed }\varepsilon
\rightarrow
\text{change inferred object radiance}
\rightarrow
\text{change inferred temperature}
}
\]

---

# 15. General derivation of temperature sensitivity to emissivity for the camera

Let:

\[
L(T)
\]

represent the blackbody radiance corresponding to the camera spectral response.

Ignoring atmosphere and a window initially:

\[
\boxed{
L_m
=
\varepsilon L(T)
+
(1-\varepsilon)L(T_r)
}
\tag{1}
\]

where:

- \(L_m\) = measured radiance,
- \(T\) = temperature inferred by the camera,
- \(T_r\) = reflected apparent temperature,
- \(\varepsilon\) = emissivity entered into the camera.

The critical point is:

\[
\boxed{L_m=\text{constant}}
\]

when we vary the emissivity setting after acquiring the measurement.

Therefore:

\[
T=T(\varepsilon)
\]

and we want:

\[
\frac{dT}{d\varepsilon}
\]

Differentiate Eq. (1) with respect to emissivity:

\[
0=
\frac{d}{d\varepsilon}
\left[
\varepsilon L(T)
+
(1-\varepsilon)L(T_r)
\right]
\]

Apply the product rule:

\[
\frac{d}{d\varepsilon}
\left[
\varepsilon L(T)
\right]
=
L(T)
+
\varepsilon
\frac{dL}{dT}
\frac{dT}{d\varepsilon}
\]

and:

\[
\frac{d}{d\varepsilon}
\left[
(1-\varepsilon)L(T_r)
\right]
=
-L(T_r)
\]

Therefore:

\[
0=
L(T)
+
\varepsilon
\frac{dL}{dT}
\frac{dT}{d\varepsilon}
-
L(T_r)
\]

Rearrange:

\[
\varepsilon
\frac{dL}{dT}
\frac{dT}{d\varepsilon}
=
-
\left[
L(T)-L(T_r)
\right]
\]

Hence:

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
L(T)-L(T_r)
}{
\varepsilon
\dfrac{dL}{dT}
}
}
\tag{2}
\]

At the correct operating point:

\[
T=T_s
\]

and:

\[
\varepsilon=\varepsilon_t
\]

so:

\[
\boxed{
\left.
\frac{\partial T_{\mathrm{camera}}}{\partial\varepsilon}
\right|_{\varepsilon_t}
=
-
\frac{
L(T_s)-L(T_r)
}{
\varepsilon_t
\left.
\dfrac{dL}{dT}
\right|_{T_s}
}
}
\tag{3}
\]

This is the general local temperature sensitivity to emissivity.

---

# 16. How the Stefan–Boltzmann result comes out of the general expression

The camera-band expression is:

\[
\frac{dT}{d\varepsilon}
=
-
\frac{
L(T_s)-L(T_r)
}{
\varepsilon
\dfrac{dL}{dT}
}
\]

For total hemispherical radiation:

\[
L(T)=CT^4
\]

where \(C\) is a constant.

Then:

\[
\frac{dL}{dT}=4CT^3
\]

Substitute:

\[
\frac{dT}{d\varepsilon}
=
-
\frac{
CT_s^4-CT_r^4
}{
\varepsilon(4CT_s^3)
}
\]

Cancel \(C\):

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
T_s^4-T_r^4
}{
4\varepsilon T_s^3
}
}
\]

Therefore the Stefan–Boltzmann sensitivity is not a competing formula.

It is a special case of the general radiance sensitivity when:

\[
L(T)\propto T^4
\]

The \(1/4\) factor appears because:

\[
\frac{d(T^4)}{dT}=4T^3
\]

For a finite-band IR camera:

\[
L(T)\neq CT^4
\]

so there is no explicit universal \(1/4\) factor.

---

# 17. Simple intuitive approximation

For modest temperature differences, a rough Stefan–Boltzmann sensitivity can be written as:

\[
\boxed{
\Delta T_{\mathrm{error}}
\approx
-
\frac{
T_s-T_{\mathrm{refl}}
}{
\varepsilon
}
\Delta\varepsilon
}
\]

This is useful only as a quick intuition.

For accurate FLIR A700 sensitivity, the band-limited radiometric model is preferable.

---

# 18. Example at \(100^\circ\mathrm{C}\)

For illustration:

\[
T_s=100^\circ C
\]

\[
T_{\mathrm{refl}}=25^\circ C
\]

\[
\varepsilon=0.9
\]

A small emissivity error of:

\[
\Delta\varepsilon=0.01
\]

can result in a temperature error on the order of fractions of a degree to around a degree depending on the exact spectral model.

The sensitivity increases when:

- surface temperature is higher,
- emissivity is lower,
- the object is much hotter or colder than the reflected surroundings.

---

# 19. Example trends previously discussed

Approximate total-spectrum sensitivities discussed were:

| True surface temperature | Emissivity | Emissivity error | Approximate temperature sensitivity |
|---:|---:|---:|---:|
| \(50^\circ C\) | 0.9 | 0.01 | \(\sim0.25^\circ C\) |
| \(100^\circ C\) | 0.9 | 0.01 | \(\sim0.61^\circ C\) |
| \(200^\circ C\) | 0.9 | 0.01 | \(\sim1.11^\circ C\) |
| \(100^\circ C\) | 0.5 | 0.01 | \(\sim1.11^\circ C\) |
| \(100^\circ C\) | 0.3 | 0.01 | \(\sim1.84^\circ C\) |
| \(200^\circ C\) | 0.3 | 0.01 | \(\sim3.32^\circ C\) |

These were illustrative simplified calculations and not exact A700 firmware values.

---

# 20. Practical meaning of unknown emissivity

The central problem is:

\[
\boxed{
\text{Unknown emissivity}
\Rightarrow
\text{unknown partition between emitted and reflected radiation}
}
\]

For high-emissivity surfaces this is usually manageable.

For low-emissivity surfaces, reflected surroundings may dominate.

Therefore emissivity uncertainty can be more important than the nominal thermal-camera accuracy.

---

# 21. How to determine emissivity experimentally

A useful practical method is to create a high-emissivity reference patch on the same surface.

For example:

- high-emissivity tape,
- high-emissivity coating,
- another reference surface with known emissivity.

If the patch is:

- thin,
- well thermally coupled,
- at essentially the same physical temperature as the surrounding surface,

then:

\[
T_{\mathrm{reference}}
\approx
T_{\mathrm{surface}}
\]

Measure the reference area with the camera using the known emissivity.

Then measure the neighboring unknown surface and adjust its emissivity setting until:

\[
T_{\mathrm{unknown}}
=
T_{\mathrm{reference}}
\]

The resulting value is an effective emissivity for that measurement condition.

This is generally more reliable than taking a generic emissivity value from a table.

---

# 22. FLIR A700-specific considerations

For the FLIR A700 family:

- detector type: uncooled microbolometer,
- approximate spectral range:

\[
\boxed{7.5-14\ \mu\mathrm{m}}
\]

- emissivity is adjustable over a wide range,
- reflected apparent temperature is separately entered,
- atmospheric correction can include:
  - target distance,
  - atmospheric temperature,
  - relative humidity,
- external optics / IR window corrections can also be entered.

The camera therefore uses emissivity as part of a radiometric inversion rather than simply multiplying a displayed temperature afterward.

---

# 23. Local emissivity parameters in FLIR measurement tools

A radiometric FLIR image can support measurement regions or tools with different object parameters.

For different parts of a scene, one may use different emissivity values, for example:

\[
\text{painted housing}: \varepsilon=0.95
\]

\[
\text{oxidized metal}: \varepsilon=0.75
\]

\[
\text{shiny metal}: \varepsilon=0.15
\]

This becomes relevant when different materials appear in the same A700 image.

---

# 24. Main numerical case

We now focus on the main case:

\[
\boxed{
T_s=450^\circ C
}
\]

\[
\boxed{
T_{\mathrm{refl}}=60^\circ C
}
\]

\[
\boxed{
\varepsilon_{\mathrm{true}}=0.85
}
\]

Convert to kelvin:

\[
T_s=723.15\ \mathrm{K}
\]

\[
T_{\mathrm{refl}}=333.15\ \mathrm{K}
\]

Initially assume negligible atmospheric attenuation:

\[
\tau\approx1
\]

so that the emissivity effect can be isolated.

---

# 25. A700-band blackbody radiance at \(450^\circ C\)

Using:

\[
L_b(T)=
\int_{7.5\mu m}^{14\mu m}
B_\lambda(T)\,d\lambda
\]

the approximate band-integrated blackbody radiance at:

\[
T_s=723.15\ \mathrm{K}
\]

was calculated as:

\[
\boxed{
L_s
=
L_b(723.15K)
\approx
1153.99\ \mathrm{W/(m^2\,sr)}
}
\]

For the surroundings:

\[
T_r=333.15\ \mathrm{K}
\]

the approximate band radiance is:

\[
\boxed{
L_r
=
L_b(333.15K)
\approx
95.05\ \mathrm{W/(m^2\,sr)}
}
\]

---

# 26. Radiation actually seen from an \(\varepsilon=0.85\) surface

With:

\[
\varepsilon=0.85
\]

the measured radiance is:

\[
L_{\mathrm{measured}}
=
\varepsilon L_s
+
(1-\varepsilon)L_r
\]

Substitute:

\[
L_{\mathrm{measured}}
=
0.85(1153.99)
+
0.15(95.05)
\]

The emitted component is:

\[
0.85(1153.99)=980.89
\]

The reflected component is:

\[
0.15(95.05)=14.26
\]

Therefore:

\[
\boxed{
L_{\mathrm{measured}}
\approx
995.15\ \mathrm{W/(m^2\,sr)}
}
\]

This is the detector-side radiometric quantity that remains fixed when we change only the emissivity setting.

---

# 27. Correct emissivity entered: \(\varepsilon=0.85\)

The camera solves:

\[
L_{\mathrm{obj}}
=
\frac{
L_{\mathrm{measured}}
-
(1-\varepsilon)L_r
}{
\varepsilon
}
\]

Substitute:

\[
L_{\mathrm{obj}}
=
\frac{
995.15
-
0.15(95.05)
}{
0.85
}
\]

\[
=
\frac{
995.15-14.26
}{
0.85
}
\]

\[
=
1153.99
\]

This corresponds to:

\[
\boxed{
T=450^\circ C
}
\]

as expected.

---

# 28. If the entered emissivity is 0.80

The detector signal is unchanged:

\[
L_{\mathrm{measured}}
=
995.15
\]

The camera now assumes:

\[
\varepsilon=0.80
\]

Therefore:

\[
L_{\mathrm{obj,0.80}}
=
\frac{
995.15
-
(1-0.80)(95.05)
}{
0.80
}
\]

\[
=
\frac{
995.15-19.01
}{
0.80
}
\]

\[
=
1220.18\ \mathrm{W/(m^2\,sr)}
\]

Inverting the 7.5-14 \(\mu\mathrm m\) Planck-band relationship gives approximately:

\[
\boxed{
T_{\mathrm{indicated}}
\approx
467.3^\circ C
}
\]

Therefore:

\[
\boxed{
\Delta T
\approx
+17.3^\circ C
}
\]

So:

\[
\boxed{
\varepsilon_{\mathrm{true}}=0.85,\qquad
\varepsilon_{\mathrm{entered}}=0.80
}
\]

makes a true \(450^\circ C\) surface appear approximately:

\[
\boxed{
467^\circ C
}
\]

in this idealized A700-band calculation.

---

# 29. If the entered emissivity is 0.90

Again:

\[
L_{\mathrm{measured}}
=
995.15
\]

Now:

\[
\varepsilon=0.90
\]

Therefore:

\[
L_{\mathrm{obj,0.90}}
=
\frac{
995.15
-
(1-0.90)(95.05)
}{
0.90
}
\]

\[
=
\frac{
995.15-9.505
}{
0.90
}
\]

\[
=
1095.16\ \mathrm{W/(m^2\,sr)}
\]

This corresponds approximately to:

\[
\boxed{
T_{\mathrm{indicated}}
\approx
434.4^\circ C
}
\]

Therefore:

\[
\boxed{
\Delta T
\approx
-15.6^\circ C
}
\]

---

# 30. Summary table for the main A700-band example

| Actual emissivity | Entered emissivity | True temperature | Calculated temperature | Error |
|---:|---:|---:|---:|---:|
| 0.85 | 0.80 | \(450^\circ C\) | \(467.3^\circ C\) | \(+17.3^\circ C\) |
| 0.85 | 0.85 | \(450^\circ C\) | \(450.0^\circ C\) | \(0^\circ C\) |
| 0.85 | 0.90 | \(450^\circ C\) | \(434.4^\circ C\) | \(-15.6^\circ C\) |

Thus approximately:

\[
\boxed{
\varepsilon=0.85\pm0.05
}
\]

gives:

\[
\boxed{
T\approx
450^{+17}_{-16}\ ^\circ C
}
\]

for this idealized A700-band case.

---

# 31. Local A700-band sensitivity at \(450^\circ C\)

The general sensitivity is:

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
L(T)-L(T_r)
}{
\varepsilon
\dfrac{dL}{dT}
}
}
\]

At:

\[
T=723.15\ \mathrm{K}
\]

the calculated band-radiance slope was approximately:

\[
\boxed{
\left.
\frac{dL}{dT}
\right|_{723.15K}
\approx
3.8023\ 
\mathrm{W/(m^2\,sr\,K)}
}
\]

The radiance difference is:

\[
L(T)-L(T_r)
=
1153.99-95.05
=
1058.94
\]

Therefore:

\[
\frac{dT}{d\varepsilon}
=
-
\frac{
1058.94
}{
0.85(3.8023)
}
\]

The denominator is:

\[
0.85(3.8023)
=
3.2320
\]

Therefore:

\[
\boxed{
\frac{dT}{d\varepsilon}
\approx
-327.6\ \mathrm{K}
\text{ per unit emissivity}
}
\]

or practically:

\[
\boxed{
\Delta\varepsilon=0.01
\Rightarrow
\Delta T\approx-3.28^\circ C
}
\]

locally around:

\[
T=450^\circ C,\qquad
\varepsilon=0.85
\]

This is the local A700-band emissivity sensitivity.

---

# 32. Why the local derivative and the \(\pm0.05\) exact result are not perfectly symmetric

The local derivative gives:

\[
\Delta T
\approx
\frac{dT}{d\varepsilon}\Delta\varepsilon
\]

This is a linear approximation valid for small changes.

For:

\[
\Delta\varepsilon=\pm0.01
\]

it is very useful.

For:

\[
\Delta\varepsilon=\pm0.05
\]

the relationship is nonlinear enough that solving the full radiometric equation is preferable.

That is why:

\[
0.85\rightarrow0.80
\]

gave:

\[
+17.3^\circ C
\]

while:

\[
0.85\rightarrow0.90
\]

gave:

\[
-15.6^\circ C
\]

rather than perfectly symmetric values.

---

# 33. Stefan–Boltzmann sensitivity for the same \(450^\circ C\) case

For:

\[
T=723.15\ \mathrm{K}
\]

\[
T_r=333.15\ \mathrm{K}
\]

\[
\varepsilon=0.85
\]

the full Stefan–Boltzmann sensitivity is:

\[
\frac{dT}{d\varepsilon}
=
-
\frac{
T^4-T_r^4
}{
4\varepsilon T^3
}
\]

or:

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-\frac{T}{4\varepsilon}
\left[
1-
\left(
\frac{T_r}{T}
\right)^4
\right]
}
\]

First, the simple high-temperature approximation:

\[
-\frac{T}{4\varepsilon}
=
-
\frac{
723.15
}{
4(0.85)
}
\]

\[
=
\boxed{
-212.7\ \mathrm{K}
\text{ per unit emissivity}
}
\]

Including the \(60^\circ C\) surroundings:

\[
\frac{dT}{d\varepsilon}
=
-
\frac{
723.15
}{
4(0.85)
}
\left[
1-
\left(
\frac{333.15}{723.15}
\right)^4
\right]
\]

giving approximately:

\[
\boxed{
-203.1\ \mathrm{K}
\text{ per unit emissivity}
}
\]

or:

\[
\boxed{
\Delta\varepsilon=0.01
\Rightarrow
\Delta T\approx-2.03^\circ C
}
\]

This is significantly smaller than the A700-band sensitivity:

\[
\boxed{
\Delta\varepsilon=0.01
\Rightarrow
\Delta T_{\mathrm{A700}}
\approx-3.28^\circ C
}
\]

The reason is the finite camera spectral band.

---

# 34. Exact Stefan–Boltzmann result for \(\varepsilon=0.80,0.85,0.90\)

Using:

\[
T_{\mathrm{reported}}
=
\left[
T_r^4+
\frac{\varepsilon_t}{\varepsilon_a}
\left(
T_s^4-T_r^4
\right)
\right]^{1/4}
\]

for:

\[
T_s=450^\circ C
\]

\[
T_r=60^\circ C
\]

\[
\varepsilon_t=0.85
\]

the approximate total-spectrum results discussed were:

| Entered emissivity | Reported temperature |
|---:|---:|
| 0.80 | \(\sim460.6^\circ C\) |
| 0.85 | \(450.0^\circ C\) |
| 0.90 | \(\sim440.2^\circ C\) |

Therefore the total-spectrum Stefan–Boltzmann errors are approximately:

\[
\boxed{
0.80
\Rightarrow
+10.6^\circ C
}
\]

\[
\boxed{
0.90
\Rightarrow
-9.8^\circ C
}
\]

These are not the preferred values for predicting A700 temperature uncertainty.

They are useful for total radiative heat-transfer reasoning.

---

# 35. Which sensitivity should be used?

This is the most important practical conclusion.

## 35.1 For wafer heat-transfer calculations

Use the Stefan–Boltzmann-based sensitivity:

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
T^4-T_r^4
}{
4\varepsilon T^3
}
}
\]

or, if:

\[
T\gg T_r
\]

use the approximation:

\[
\boxed{
\frac{dT}{d\varepsilon}
\approx
-\frac{T}{4\varepsilon}
}
\]

This belongs to a total-radiation heat-transfer model.

---

## 35.2 For uncertainty in the temperature reported by the FLIR A700

Use the band-limited radiometric sensitivity:

\[
\boxed{
\frac{dT_{\mathrm{FLIR}}}{d\varepsilon}
=
-
\frac{
L_b(T)-L_b(T_r)
}{
\varepsilon
\dfrac{dL_b}{dT}
}
}
\]

where:

\[
L_b(T)
=
\int_{7.5\mu m}^{14\mu m}
B_\lambda(T)\,d\lambda
\]

For the main case:

\[
T=450^\circ C
\]

\[
T_r=60^\circ C
\]

\[
\varepsilon=0.85
\]

this gives approximately:

\[
\boxed{
\frac{dT_{\mathrm{FLIR}}}{d\varepsilon}
\approx
-328^\circ C
\text{ per unit emissivity}
}
\]

or:

\[
\boxed{
0.01\text{ emissivity change}
\approx
3.3^\circ C\text{ temperature change}
}
\]

This is the sensitivity that should be used when the question is:

> How much does emissivity uncertainty change the A700-reported wafer temperature?

---

# 36. Silicon-processing chamber geometry

The discussed application is:

- a cylindrical processing chamber,
- a hot silicon wafer near the bottom,
- a lid at the top,
- a FLIR camera centered in the top lid,
- the camera looking downward toward the wafer.

A conceptual layout is:

```text
                 FLIR A700
                    |
                    v
             ----------------
             |    top lid    |
             |       o       |
             |               |
             |               |
             | cylindrical   |
             |   chamber     |
             |               |
             |               |
             |   hot wafer   |
             |   ~450 C      |
             ----------------
```

The centered camera geometry is advantageous because the wafer center is viewed nearly normally.

---

# 37. Viewing angle across the wafer

At the wafer center:

\[
\theta\approx0^\circ
\]

Toward the edge:

\[
\boxed{
\theta(r)
=
\tan^{-1}
\left(
\frac{r}{H}
\right)
}
\]

where:

- \(r\) = radial position on the wafer,
- \(H\) = camera-to-wafer vertical distance.

The relevant wafer emissivity may therefore depend on:

\[
\boxed{
\varepsilon
=
\varepsilon(
\lambda,
T,
\theta,
\text{surface condition},
\text{doping},
\text{oxide/coating}
)
}
\]

This matters because a single global emissivity may create an apparent radial temperature variation if the directional emissivity changes with viewing angle.

---

# 38. Reflected apparent temperature in a chamber is not necessarily wall temperature

A central chamber-specific issue is that:

\[
\boxed{
T_{\mathrm{refl}}
\neq
\text{automatically the measured wall temperature}
}
\]

even if the wall thermocouple reads:

\[
60^\circ C
\]

The wafer sees irradiation from the entire enclosure.

The chamber may involve:

\[
\text{wafer}
\rightarrow
\text{wall}
\rightarrow
\text{wafer}
\rightarrow
\text{camera}
\]

and multiple reflections.

Therefore it is more correct to think in terms of an effective reflected apparent temperature:

\[
\boxed{
T_{\mathrm{refl,eff}}
}
\]

If the entire enclosure is approximately:

- isothermal,
- diffuse,
- high-emissivity,

then:

\[
T_{\mathrm{refl,eff}}
\approx
T_{\mathrm{wall}}
\]

may be a reasonable approximation.

If the walls are:

- shiny,
- metallic,
- low-emissivity,
- non-isothermal,

then the approximation can be poor.

---

# 39. Reflected chamber radiation should ideally be modeled radiometrically

A more complete model for the radiance leaving the wafer toward the camera is:

\[
\boxed{
L_{\mathrm{wafer,out}}
=
\varepsilon_\lambda(\theta)
B_\lambda(T_w)
+
\rho_\lambda(\theta)
L_{\mathrm{chamber},\lambda}
}
\]

For an opaque surface:

\[
\rho_\lambda
\approx
1-\varepsilon_\lambda
\]

The camera then receives this through whatever gas and window lie in the optical path.

---

# 40. Chamber view-factor and multiple-reflection issue

In a cylindrical processing chamber, the irradiation incident on the wafer depends on:

- chamber diameter,
- chamber height,
- wafer diameter,
- wafer position,
- lid temperature,
- sidewall temperature,
- wall emissivity,
- lid emissivity,
- wafer emissivity,
- specular versus diffuse reflection behavior.

For a rigorous model, chamber radiosity or ray-tracing methods may be needed.

This is particularly relevant in semiconductor processing chambers and rapid thermal processing systems.

---

# 41. IR window / viewport

If the A700 is outside the process environment and looks through an IR window, the window must be included.

The detector-side radiance is then conceptually:

\[
\boxed{
L_{\mathrm{detector}}
=
f(
\tau_{\mathrm{window}},
T_{\mathrm{window}},
\tau_{\mathrm{gas}},
T_{\mathrm{gas}},
\varepsilon_{\mathrm{wafer}},
T_{\mathrm{wafer}},
L_{\mathrm{refl}}
)
}
\]

The window:

- transmits some wafer radiation,
- reflects some radiation,
- emits its own radiation.

Therefore one should account for:

- window material,
- window thickness,
- window temperature,
- spectral transmission,
- viewing angle,
- contamination / coating deposition.

The A700 supports external optics / window corrections.

---

# 42. Process gas

The process gas can also matter if it:

- absorbs in the camera spectral band,
- emits infrared radiation,
- contains species with strong absorption bands,
- is at elevated pressure or temperature.

If the optical path is short and the gas is highly transparent in 7.5-14 \(\mu\mathrm m\), the correction may be small.

But this should be checked for the actual process gas and pressure.

---

# 43. Full conceptual spectral model for the chamber

A more rigorous camera model is:

\[
\boxed{
L_{\mathrm{A700}}
=
\int
R_{\mathrm{A700}}(\lambda)
\left[
\varepsilon_\lambda(\theta)
B_\lambda(T_w)
+
\rho_\lambda(\theta)
L_{\mathrm{chamber},\lambda}
\right]
d\lambda
}
\]

with additional multiplication and emission terms for:

- gas transmission,
- gas emission,
- window transmission,
- window emission,
- optical transmission.

Here:

- \(R_{\mathrm{A700}}(\lambda)\) = actual camera spectral response,
- \(B_\lambda(T_w)\) = Planck radiance of the wafer,
- \(\varepsilon_\lambda(\theta)\) = spectral directional wafer emissivity,
- \(\rho_\lambda(\theta)\) = spectral directional reflectivity,
- \(L_{\mathrm{chamber},\lambda}\) = chamber irradiation spectrum.

This is more physically complete than using a single gray emissivity and a single wall temperature.

---

# 44. Important distinction: heat-transfer model versus camera model

This should not be mixed.

## Heat-transfer model

For radiation heat transfer:

\[
\boxed{
q''_{\mathrm{rad}}
=
\varepsilon\sigma
\left(
T_w^4-T_{\mathrm{sur}}^4
\right)
}
\]

or a more complete enclosure-radiation model.

This is a total-spectrum model.

The relevant sensitivity contains the \(1/4\) factor when inverted for temperature.

---

## Camera measurement model

For the FLIR A700:

\[
\boxed{
L_b(T)
=
\int_{7.5\mu m}^{14\mu m}
B_\lambda(T)\,d\lambda
}
\]

and:

\[
\boxed{
\frac{dT_{\mathrm{FLIR}}}{d\varepsilon}
=
-
\frac{
L_b(T)-L_b(T_r)
}{
\varepsilon
\dfrac{dL_b}{dT}
}
}
\]

This is the preferred emissivity sensitivity for the A700 reported temperature.

---

# 45. When is \(-T/(4\varepsilon)\) acceptable?

The approximation:

\[
\boxed{
\frac{dT}{d\varepsilon}
\approx
-\frac{T}{4\varepsilon}
}
\]

is acceptable when:

1. one is using total-spectrum Stefan–Boltzmann radiation,
2. reflected surroundings are negligible compared with object temperature,
3. one wants a rough thermal-radiation sensitivity,
4. one is not trying to reproduce the finite-band response of a thermal camera.

For the main case:

\[
T=723.15K
\]

\[
T_r=333.15K
\]

the simple approximation gives:

\[
-212.7\ \mathrm{K/emissivity}
\]

and the full total-spectrum expression gives:

\[
-203.1\ \mathrm{K/emissivity}
\]

So the simple approximation is within roughly 5% of the full Stefan–Boltzmann expression for this temperature ratio.

However, it is not the correct A700 camera sensitivity.

---

# 46. Recommended sensitivity for this silicon-processing application

If the objective is:

> quantify uncertainty in the FLIR A700 measured wafer temperature due to uncertainty in wafer emissivity,

then use:

\[
\boxed{
S_\varepsilon
=
\frac{\partial T_{\mathrm{FLIR}}}{\partial\varepsilon}
=
-
\frac{
L_b(T_w)-L_b(T_{\mathrm{refl,eff}})
}{
\varepsilon
\dfrac{dL_b}{dT_w}
}
}
\]

For the initial simplified case:

\[
T_w=450^\circ C
\]

\[
T_{\mathrm{refl,eff}}=60^\circ C
\]

\[
\varepsilon=0.85
\]

the estimate is:

\[
\boxed{
S_\varepsilon
\approx
-328^\circ C
\text{ per unit emissivity}
}
\]

or:

\[
\boxed{
\Delta\varepsilon=0.01
\Rightarrow
|\Delta T|
\approx
3.3^\circ C
}
\]

near the operating point.

---

# 47. Important uncertainty sources for the real chamber

For the actual chamber, important uncertainties include:

1. wafer emissivity,
2. directional dependence of wafer emissivity,
3. spectral dependence of wafer emissivity,
4. wafer temperature dependence of emissivity,
5. oxide or process-film thickness,
6. doping effects,
7. chamber effective reflected radiation,
8. sidewall emissivity,
9. lid emissivity,
10. chamber geometry,
11. multiple reflections,
12. viewport transmission,
13. viewport temperature,
14. viewport contamination,
15. process-gas transmission,
16. process-gas emission,
17. camera calibration,
18. detector nonuniformity,
19. spatial resolution,
20. viewing-angle effects.

In many cases the emissivity and reflected-radiation uncertainty can dominate over the nominal camera accuracy.

---

# 48. Practical hierarchy for improving the measurement

A useful order is:

1. determine the actual wafer spectral/directional emissivity as well as possible,
2. determine the effective reflected apparent temperature seen by the wafer,
3. characterize the IR viewport transmission and temperature,
4. check gas absorption/emission in 7.5-14 \(\mu\mathrm m\),
5. include the camera's calibration uncertainty,
6. verify spatial resolution and viewing angle,
7. compare the camera result against an independent temperature reference if available.

---

# 49. Recommended experimental calibration approach

If possible, calibrate the IR measurement under the actual chamber conditions.

Potential independent references include:

- thermocouple on a calibration wafer,
- instrumented wafer,
- pyrometer with known spectral calibration,
- blackbody reference,
- known high-emissivity coating or patch, if process-compatible.

The best effective emissivity for the camera may be the value that makes the A700 agree with an independent temperature reference under the actual:

- wafer temperature,
- chamber temperature,
- pressure,
- gas,
- viewport,
- viewing angle,
- process-film condition.

This effectively absorbs some real-world nonidealities into a calibrated emissivity or radiometric correction.

---

# 50. Final conclusion

For the problem discussed here:

\[
\boxed{
T_w=450^\circ C
}
\]

\[
\boxed{
T_{\mathrm{refl}}\approx60^\circ C
}
\]

\[
\boxed{
\varepsilon\approx0.85
}
\]

there are two different sensitivities.

## For total radiation / heat transfer:

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
T_w^4-T_r^4
}{
4\varepsilon T_w^3
}
}
\]

which gives approximately:

\[
\boxed{
-203^\circ C
\text{ per emissivity unit}
}
\]

or:

\[
\boxed{
0.01
\text{ emissivity change}
\approx
2.0^\circ C
}
\]

The rough approximation:

\[
\boxed{
-\frac{T}{4\varepsilon}
}
\]

gives approximately:

\[
\boxed{
-213^\circ C
\text{ per emissivity unit}
}
\]

and is reasonably close for the total-spectrum model because the wafer is much hotter than the surroundings.

---

## For FLIR A700 reported temperature:

Use:

\[
\boxed{
\frac{dT_{\mathrm{FLIR}}}{d\varepsilon}
=
-
\frac{
L_b(T_w)-L_b(T_{\mathrm{refl,eff}})
}{
\varepsilon
\dfrac{dL_b}{dT_w}
}
}
\]

For the idealized 7.5-14 \(\mu\mathrm m\) A700 band:

\[
\boxed{
\frac{dT_{\mathrm{FLIR}}}{d\varepsilon}
\approx
-328^\circ C
\text{ per emissivity unit}
}
\]

or:

\[
\boxed{
\Delta\varepsilon=0.01
\Rightarrow
\Delta T_{\mathrm{FLIR}}
\approx
-3.3^\circ C
}
\]

and the full nonlinear estimates were:

\[
\boxed{
\varepsilon_{\mathrm{entered}}=0.80
\Rightarrow
T_{\mathrm{reported}}
\approx
467.3^\circ C
}
\]

\[
\boxed{
\varepsilon_{\mathrm{entered}}=0.85
\Rightarrow
T_{\mathrm{reported}}
=
450^\circ C
}
\]

\[
\boxed{
\varepsilon_{\mathrm{entered}}=0.90
\Rightarrow
T_{\mathrm{reported}}
\approx
434.4^\circ C
}
\]

Therefore, for uncertainty in the **A700-measured wafer temperature**, the finite-band radiometric sensitivity should be used rather than:

\[
\frac{dT}{d\varepsilon}
=
-\frac{T}{4\varepsilon}
\]

The latter is a Stefan–Boltzmann total-radiation approximation and is better suited to thermal-radiation heat-transfer reasoning.

---

# 51. Most important equations collected in one place

## Planck law

\[
\boxed{
B_\lambda(T)=
\frac{2hc^2}{\lambda^5}
\frac{1}{
\exp\left(\frac{hc}{\lambda kT}\right)-1
}
}
\]

## A700 approximate band radiance

\[
\boxed{
L_b(T)
=
\int_{7.5\mu m}^{14\mu m}
B_\lambda(T)\,d\lambda
}
\]

## Opaque-surface radiance reaching camera

\[
\boxed{
L_m
=
\varepsilon L_b(T)
+
(1-\varepsilon)L_b(T_r)
}
\]

## General camera emissivity sensitivity

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
L(T)-L(T_r)
}{
\varepsilon
\dfrac{dL}{dT}
}
}
\]

## A700-band sensitivity

\[
\boxed{
\frac{dT_{\mathrm{FLIR}}}{d\varepsilon}
=
-
\frac{
L_b(T)-L_b(T_r)
}{
\varepsilon
\dfrac{dL_b}{dT}
}
}
\]

## Total-spectrum Stefan–Boltzmann sensitivity

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
T^4-T_r^4
}{
4\varepsilon T^3
}
}
\]

## High-temperature Stefan–Boltzmann approximation

\[
\boxed{
\frac{dT}{d\varepsilon}
\approx
-\frac{T}{4\varepsilon}
}
\]

## Exact total-spectrum temperature corresponding to wrong emissivity

\[
\boxed{
T_{\mathrm{reported}}
=
\left[
T_r^4
+
\frac{\varepsilon_t}{\varepsilon_a}
\left(
T_s^4-T_r^4
\right)
\right]^{1/4}
}
\]

---

# 52. Interpretation of the sign

For a hot wafer:

\[
T_w>T_r
\]

therefore:

\[
L(T_w)>L(T_r)
\]

and thus:

\[
\boxed{
\frac{dT}{d\varepsilon}<0
}
\]

This means:

\[
\boxed{
\text{Increase entered emissivity}
\Rightarrow
\text{decrease reported temperature}
}
\]

and:

\[
\boxed{
\text{Decrease entered emissivity}
\Rightarrow
\text{increase reported temperature}
}
\]

for the same measured detector signal.

---

# 53. Final practical recommendation

For the cylindrical silicon-processing chamber with a top-center FLIR A700:

- use the Stefan–Boltzmann / enclosure-radiation formulation for **wafer thermal heat-transfer calculations**,
- use the A700 band-limited radiometric formulation for **camera temperature uncertainty**,
- do not assume that the physical wall temperature is automatically the correct reflected apparent temperature,
- account for the IR window if present,
- check process-gas transmission,
- consider angular dependence of silicon-wafer emissivity across the field of view,
- and, where possible, calibrate emissivity or effective radiometric parameters against an independent temperature reference under actual chamber conditions.

The main local sensitivity estimate for the simplified stated condition remains:

\[
\boxed{
\varepsilon=0.85,\quad
T_w=450^\circ C,\quad
T_{\mathrm{refl}}=60^\circ C
}
\]

\[
\boxed{
\frac{dT_{\mathrm{FLIR}}}{d\varepsilon}
\approx
-328^\circ C
\text{ per unit emissivity}
}
\]

or:

\[
\boxed{
\Delta\varepsilon=0.01
\Rightarrow
|\Delta T_{\mathrm{FLIR}}|
\approx
3.3^\circ C
}
\]

under the idealized A700 7.5-14 \(\mu\mathrm m\) band model.

