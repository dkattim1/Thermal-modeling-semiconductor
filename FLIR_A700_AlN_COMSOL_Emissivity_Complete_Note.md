# FLIR A700, Emissivity, AlN Ceramic, and COMSOL Radiation Modeling

## 1. Scope

This note consolidates the complete discussion about infrared thermography, emissivity, the FLIR A700, aluminum nitride (AlN), COMSOL surface-to-surface radiation, and the temperature discrepancy between experiment and simulation.

The specific application considered is a hot ceramic puck / wafer assembly in a cylindrical silicon-processing chamber, with a FLIR A700 viewing the surface from the top-center of the chamber.

The principal measured/simulated values discussed are:

\[
T_{\mathrm{FLIR}} = 492^\circ\mathrm{C}
\]

with the FLIR emissivity setting

\[
\varepsilon_{\mathrm{FLIR,set}} = 0.80,
\]

and a COMSOL prediction

\[
T_{\mathrm{COMSOL}} = 515^\circ\mathrm{C}
\]

using

\[
\varepsilon_{\mathrm{COMSOL}} = 0.88.
\]

The ceramic surface is aluminum nitride (AlN).

A reflected-apparent-temperature example of

\[
T_{\mathrm{refl}} = 60^\circ\mathrm{C}
\]

is used in several sensitivity calculations. Where atmospheric, process-gas, or viewport attenuation is neglected, that assumption is stated explicitly.

---

# 2. What an infrared camera actually measures

An infrared thermal camera does **not** measure temperature directly.

It measures infrared radiation and uses a calibrated radiometric model to infer temperature:

\[
\boxed{
\text{surface temperature}
\rightarrow
\text{thermal radiation}
\rightarrow
\text{detector signal}
\rightarrow
\text{radiometric correction}
\rightarrow
\text{reported temperature}
}
\]

A real surface emits radiation according to both its temperature and its emissivity.

For a blackbody, the spectral radiance is given by Planck's law:

\[
\boxed{
B_\lambda(T)
=
\frac{2hc^2}{\lambda^5}
\frac{1}{
\exp\left(\frac{hc}{\lambda kT}\right)-1
}
}
\]

where

- \(B_\lambda\) is blackbody spectral radiance,
- \(h\) is Planck's constant,
- \(c\) is the speed of light,
- \(k\) is Boltzmann's constant,
- \(\lambda\) is wavelength,
- \(T\) is absolute temperature.

A real surface has spectral emissivity \(\varepsilon_\lambda\), so its emitted radiance is approximately

\[
\boxed{
L_{\lambda,\mathrm{emit}}
=
\varepsilon_\lambda B_\lambda(T)
}
\]

for a given direction.

---

# 3. Emissivity and reflection

For an opaque surface, transmission is approximately zero:

\[
\tau_\lambda \approx 0.
\]

Energy conservation gives

\[
\alpha_\lambda + \rho_\lambda \approx 1.
\]

Kirchhoff's law gives, under thermal equilibrium conditions,

\[
\alpha_\lambda = \varepsilon_\lambda.
\]

Therefore, approximately,

\[
\boxed{
\rho_\lambda \approx 1-\varepsilon_\lambda.
}
\]

This is why a low-emissivity surface is generally highly reflective in the same spectral region.

For infrared thermography, the camera therefore sees a combination of:

\[
\boxed{
\text{radiation emitted by the object}
+
\text{radiation from the surroundings reflected by the object}.
}
\]

Ignoring atmospheric attenuation for the moment,

\[
\boxed{
L_m
=
\varepsilon L(T)
+
(1-\varepsilon)L(T_{\mathrm{refl}})
}
\]

where

- \(L_m\) is the measured radiance,
- \(T\) is object temperature,
- \(T_{\mathrm{refl}}\) is reflected apparent temperature,
- \(\varepsilon\) is the effective emissivity relevant to the measurement.

---

# 4. FLIR A700 wavelength band

The official FLIR A700 documentation specifies:

\[
\boxed{
7.5\text{--}14.0\ \mu\mathrm{m}
}
\]

as the detector spectral range.

The A700 uses an uncooled microbolometer detector.

Therefore an idealized blackbody-band radiance for the camera can be written as

\[
\boxed{
L_b(T)
=
\int_{7.5\ \mu\mathrm{m}}^{14\ \mu\mathrm{m}}
B_\lambda(T)\,d\lambda.
}
\]

This expression assumes a flat response inside the stated band and zero response outside it.

The real camera does **not** have a perfectly rectangular spectral response. A more exact representation would be

\[
\boxed{
L_{\mathrm{eff}}(T)
=
\int
R_{\mathrm{A700}}(\lambda)
B_\lambda(T)\,d\lambda
}
\]

where \(R_{\mathrm{A700}}(\lambda)\) includes detector, lens, filter, and calibration response.

Therefore, all calculations in this note using a simple \(7.5\text{--}14\ \mu\mathrm{m}\) integral should be understood as an **idealized A700-band radiometric approximation**, not an exact reproduction of FLIR firmware.

---

# 5. How FLIR uses emissivity

FLIR's thermographic measurement model contains three principal radiative contributions:

1. emission from the object,
2. reflected radiation from the surroundings,
3. atmospheric emission.

A standard FLIR measurement equation is

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

where

- \(U_{\mathrm{tot}}\) is the measured camera signal,
- \(U_{\mathrm{obj}}\) is the signal corresponding to blackbody radiation at object temperature,
- \(U_{\mathrm{refl}}\) corresponds to reflected apparent temperature,
- \(U_{\mathrm{atm}}\) corresponds to atmospheric temperature,
- \(\tau\) is atmospheric transmission,
- \(\varepsilon\) is object emissivity.

Rearranging,

\[
\boxed{
U_{\mathrm{obj}}
=
\frac{U_{\mathrm{tot}}}{\varepsilon\tau}
-
\frac{1-\varepsilon}{\varepsilon}U_{\mathrm{refl}}
-
\frac{1-\tau}{\varepsilon\tau}U_{\mathrm{atm}}.
}
\]

The camera then converts \(U_{\mathrm{obj}}\) into object temperature using its radiometric calibration.

Therefore,

\[
\boxed{
\varepsilon_{\mathrm{FLIR}}
:
\text{measured infrared signal}
\rightarrow
\text{inferred temperature}.
}
\]

Changing the FLIR emissivity setting does **not** physically change the object. It changes how the measured radiation is interpreted.

---

# 6. Does FLIR automatically vary emissivity with temperature?

In ordinary use, the FLIR A700 does not independently determine a material-specific law

\[
\varepsilon=\varepsilon(T)
\]

and automatically update it as the target heats.

The emissivity is a user-entered radiometric parameter.

Therefore if the physical AlN surface has a temperature-dependent effective LWIR emissivity,

\[
\varepsilon_{\mathrm{AlN,A700}}(T),
\]

a fixed camera setting such as

\[
\varepsilon_{\mathrm{FLIR,set}}=0.80
\]

does not automatically follow that change.

A temperature-dependent correction must instead be established from material data, spectral modeling, or calibration against an independent temperature reference.

---

# 7. COMSOL uses emissivity for a different calculation

COMSOL surface-to-surface radiation does **not** use emissivity primarily to infer temperature from a detected radiance.

Instead, emissivity participates in the physical energy balance.

For a diffuse-gray surface, COMSOL defines radiosity as the sum of emitted and diffusely reflected radiation:

\[
\boxed{
J
=
(1-\varepsilon)G
+
\varepsilon e_b(T)
}
\]

where

- \(J\) is radiosity,
- \(G\) is irradiation incident on the surface,
- \(e_b(T)\) is total blackbody hemispherical emissive power,
- \(\varepsilon\) is surface emissivity.

For total blackbody emission,

\[
\boxed{
e_b(T)=\sigma T^4.
}
\]

The difference between irradiation and outgoing radiation contributes to the radiative heat flux in the heat-transfer problem.

Therefore,

\[
\boxed{
\varepsilon_{\mathrm{COMSOL}}
:
\text{radiative heat transfer}
\rightarrow
\text{energy balance}
\rightarrow
\text{predicted physical temperature}.
}
\]

Changing \(\varepsilon_{\mathrm{COMSOL}}\) can physically change the simulated equilibrium temperature.

---

# 8. FLIR emissivity and COMSOL emissivity are not automatically the same effective number

The underlying physical quantity is still emissivity, but the **effective value required by each calculation can differ**.

For the FLIR A700, the relevant effective emissivity is approximately a spectral-response-weighted, directional emissivity:

\[
\boxed{
\varepsilon_{\mathrm{A700}}(T,\theta)
=
\frac{
\int
R_{\mathrm{A700}}(\lambda)
\varepsilon_\lambda(T,\theta)
B_\lambda(T)\,d\lambda
}{
\int
R_{\mathrm{A700}}(\lambda)
B_\lambda(T)\,d\lambda
}.
}
\]

In the idealized rectangular-band approximation,

\[
\boxed{
\varepsilon_{\mathrm{A700}}
\approx
\frac{
\int_{7.5}^{14}
\varepsilon_\lambda
B_\lambda(T)\,d\lambda
}{
\int_{7.5}^{14}
B_\lambda(T)\,d\lambda
}.
}
\]

By contrast, a gray total-radiation emissivity appropriate to total hemispherical heat transfer is approximately

\[
\boxed{
\varepsilon_{\mathrm{total}}(T)
=
\frac{
\int_0^\infty
\varepsilon_\lambda(T)
E_{b,\lambda}(T)\,d\lambda
}{
\sigma T^4
}.
}
\]

Therefore,

\[
\boxed{
\varepsilon_{\mathrm{A700}}
\neq
\varepsilon_{\mathrm{COMSOL}}
}
\]

in general.

They become equal only under a gray-surface approximation where emissivity is effectively independent of wavelength and, depending on the model, direction.

---

# 9. What happens if the same emissivity really is valid for both?

Suppose a material is genuinely diffuse, gray, opaque, and has

\[
\varepsilon=0.88
\]

for both total radiation and the A700 spectral band.

If all other physics and measurement corrections are correct, then both COMSOL and FLIR should converge toward the same physical temperature.

For example, if the true temperature is

\[
T_{\mathrm{true}}=500^\circ\mathrm{C},
\]

then a correct model could give

\[
T_{\mathrm{COMSOL}}\approx500^\circ\mathrm{C}
\]

and

\[
T_{\mathrm{FLIR}}\approx500^\circ\mathrm{C}.
\]

The fact that FLIR sees only \(7.5\text{--}14\ \mu\mathrm{m}\) does **not** intrinsically make its temperature lower.

The camera is calibrated to convert the radiance in its own spectral band into temperature.

---

# 10. What happens if the correct emissivities differ?

Suppose the same physical AlN surface has

\[
\varepsilon_{\mathrm{total}}=0.88
\]

for total hemispherical heat transfer but

\[
\varepsilon_{\mathrm{A700}}=0.80
\]

in the A700 band and viewing direction.

Then the physically appropriate settings could be

\[
\boxed{
\varepsilon_{\mathrm{COMSOL}}=0.88
}
\]

and

\[
\boxed{
\varepsilon_{\mathrm{FLIR}}=0.80.
}
\]

If both values are correct and all other assumptions are correct, COMSOL and FLIR can still report the same true temperature.

Thus the correct objective is **not**

\[
\varepsilon_{\mathrm{FLIR}}
=
\varepsilon_{\mathrm{COMSOL}},
\]

but rather

\[
\boxed{
\varepsilon_{\mathrm{FLIR}}
=
\text{correct A700-band effective emissivity}
}
\]

and

\[
\boxed{
\varepsilon_{\mathrm{COMSOL}}
=
\text{correct emissivity for the radiation heat-transfer model}.
}
\]

---

# 11. COMSOL can also model spectral emissivity

COMSOL's diffuse-gray model assumes emissivity is independent of wavelength.

COMSOL also provides diffuse-spectral radiation modeling, in which surface properties may depend on wavelength and temperature:

\[
\boxed{
\varepsilon=\varepsilon(\lambda,T).
}
\]

The spectrum can be divided into multiple bands with separate emissivities.

This is relevant for AlN because its infrared optical properties are not spectrally flat.

If sufficiently reliable AlN spectral emissivity data are available, a spectral COMSOL model is physically preferable to forcing one gray value over the whole spectrum.

---

# 12. Wien's law and the AlN ceramic puck

Wien's displacement law for a blackbody is

\[
\boxed{
\lambda_{\max}T=b
}
\]

with approximately

\[
b=2898\ \mu\mathrm{m\,K}.
\]

At

\[
T=492^\circ\mathrm{C}=765.15\ \mathrm{K},
\]

the blackbody peak is

\[
\lambda_{\max}
=
\frac{2898}{765.15}
\approx
\boxed{
3.79\ \mu\mathrm{m}.
}
\]

At

\[
T=515^\circ\mathrm{C}=788.15\ \mathrm{K},
\]

\[
\lambda_{\max}
=
\frac{2898}{788.15}
\approx
\boxed{
3.68\ \mu\mathrm{m}.
}
\]

The A700 band begins at

\[
7.5\ \mu\mathrm{m}.
\]

Therefore the A700 does not include the blackbody peak at these temperatures.

However, this does **not** mean Wien's law alone determines the actual peak of radiation from AlN.

For a real surface,

\[
\boxed{
L_\lambda^{\mathrm{real}}
=
\varepsilon_\lambda B_\lambda(T).
}
\]

If \(\varepsilon_\lambda\) varies strongly with wavelength, the maximum of the real emitted spectrum can shift relative to the blackbody peak.

Wien's law is therefore useful for understanding the blackbody weighting, but it does not by itself specify the spectral emissivity or the actual AlN emission maximum.

---

# 13. Is the A700 "missing" the thermal radiation?

It misses the blackbody spectral peak near \(3.7\text{--}3.8\ \mu\mathrm{m}\), but it still detects substantial thermal radiation in its own band.

Using Planck integration, the fraction of total blackbody radiant power in \(7.5\text{--}14\ \mu\mathrm{m}\) is approximately

\[
\boxed{
21.3\%
}
\]

at \(492^\circ\mathrm{C}\), and approximately

\[
\boxed{
20.2\%
}
\]

at \(515^\circ\mathrm{C}\).

Thus the A700 sees roughly one-fifth of the total blackbody radiant power at these temperatures.

That is sufficient for calibrated radiometric thermography.

The important question is not whether the camera sees the blackbody peak; it is whether the surface emissivity in the camera band differs substantially from its total-radiation emissivity.

---

# 14. Why wavelength can change the effective emissivity of AlN

AlN has wavelength-dependent infrared optical properties associated with lattice vibrations and its Reststrahlen / phonon-polariton region.

Published optical work reports strong AlN infrared behavior in approximately the \(11\text{--}16\ \mu\mathrm{m}\) range, overlapping the upper part of the A700 \(7.5\text{--}14\ \mu\mathrm{m}\) band.

This makes it physically plausible for

\[
\varepsilon_{\mathrm{A700}}
\]

to differ from

\[
\varepsilon_{\mathrm{total}}.
\]

However, the exact difference cannot be determined from the wavelength band alone.

Actual ceramic emissivity depends on

- composition,
- sintering additives,
- porosity,
- roughness,
- oxidation,
- surface finish,
- coating or deposited films,
- temperature,
- viewing angle,
- wavelength.

Therefore the difference

\[
0.80 \quad \text{versus} \quad 0.88
\]

is physically possible, but cannot be attributed uniquely to spectral weighting without spectral emissivity data for the actual AlN puck.

---

# 15. Experimental evidence for temperature-dependent LWIR emissivity of AlN

Published quantitative-thermography studies have specifically measured the **normal LWIR apparent emissivity of aluminum nitride ceramic** over approximately

\[
40^\circ\mathrm{C}
\text{ to }
550^\circ\mathrm{C}.
\]

The reported conclusion is that AlN's apparent LWIR emissivity has a strong temperature dependence.

The work also demonstrated that apparent emissivity depends on both

\[
\varepsilon_\lambda(T)
\]

and the spectral response of the infrared camera.

This supports the conclusion that a fixed FLIR emissivity such as

\[
\varepsilon=0.80
\]

should be treated as an assumption requiring validation for AlN at approximately \(500^\circ\mathrm{C}\), rather than a universally valid material constant.

---

# 16. Derivation of FLIR temperature sensitivity to emissivity

This is the central derivation.

Ignore atmosphere and window terms temporarily to isolate emissivity.

For a fixed measured radiance,

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

where \(T_r\) is reflected apparent temperature.

Changing the emissivity setting after acquiring the image does not change \(L_m\).

Thus

\[
\boxed{
\frac{dL_m}{d\varepsilon}=0.
}
\]

Differentiate Eq. (1):

\[
0
=
\frac{d}{d\varepsilon}
\left[
\varepsilon L(T)
+
(1-\varepsilon)L(T_r)
\right].
\]

Using the product rule,

\[
0
=
L(T)
+
\varepsilon
\frac{dL}{dT}
\frac{dT}{d\varepsilon}
-
L(T_r).
\]

Therefore

\[
\varepsilon
\frac{dL}{dT}
\frac{dT}{d\varepsilon}
=
-
\left[
L(T)-L(T_r)
\right].
\]

Hence

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
L(T)-L(T_r)
}{
\varepsilon\dfrac{dL}{dT}
}.
}
\tag{2}
\]

For the ideal A700-band model,

\[
\boxed{
L(T)
=
L_b(T)
=
\int_{7.5\mu m}^{14\mu m}B_\lambda(T)\,d\lambda.
}
\]

Therefore,

\[
\boxed{
\frac{dT_{\mathrm{A700}}}{d\varepsilon}
=
-
\frac{
L_b(T)-L_b(T_r)
}{
\varepsilon\dfrac{dL_b}{dT}
}.
}
\tag{3}
\]

The negative sign means that, for a hot target,

\[
\boxed{
\varepsilon_{\mathrm{set}}\uparrow
\quad\Rightarrow\quad
T_{\mathrm{reported}}\downarrow
}
\]

for the same detector signal.

---

# 17. Where the Stefan--Boltzmann \(1/4\) factor comes from

For total-spectrum blackbody radiation,

\[
L(T)=CT^4,
\]

where \(C\) is a constant depending on whether radiance or hemispherical emissive power is being used.

Then

\[
\frac{dL}{dT}=4CT^3.
\]

Substitute this into Eq. (2):

\[
\frac{dT}{d\varepsilon}
=
-
\frac{
CT^4-CT_r^4
}{
\varepsilon(4CT^3)
}.
\]

Cancel \(C\):

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
T^4-T_r^4
}{
4\varepsilon T^3
}.
}
\tag{4}
\]

Equivalently,

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-\frac{T}{4\varepsilon}
\left[
1-\left(\frac{T_r}{T}\right)^4
\right].
}
\]

If

\[
T\gg T_r,
\]

then

\[
\left(\frac{T_r}{T}\right)^4\ll1,
\]

so

\[
\boxed{
\frac{dT}{d\varepsilon}
\approx
-\frac{T}{4\varepsilon}.
}
\tag{5}
\]

Thus the familiar \(1/4\) appears specifically because total blackbody radiation scales as \(T^4\).

For the finite A700 spectral band, the radiance is not proportional to \(T^4\), so the general derivative in Eq. (3) should be used instead.

---

# 18. How the band-radiance slope \(dL_b/dT \approx 3.8\) was calculated

At

\[
T=450^\circ\mathrm{C}=723.15\ \mathrm{K},
\]

the idealized A700-band blackbody radiance is

\[
L_b(T)
=
\int_{7.5\mu m}^{14\mu m}
B_\lambda(T)\,d\lambda.
\]

Numerical Planck integration gives

\[
\boxed{
L_b(723.15\ \mathrm{K})
\approx
1153.9902\
\mathrm{W\,m^{-2}\,sr^{-1}}.
}
\]

The slope is

\[
\boxed{
\left.
\frac{dL_b}{dT}
\right|_{723.15\mathrm{K}}.
}
\]

A simple numerical central difference with

\[
\Delta T=1\ \mathrm{K}
\]

is

\[
\boxed{
\frac{dL_b}{dT}
\approx
\frac{
L_b(T+\Delta T)-L_b(T-\Delta T)
}{
2\Delta T
}.
}
\]

At

\[
T-1=722.15\ \mathrm{K},
\]

\[
L_b(722.15)
\approx
1150.1897\
\mathrm{W\,m^{-2}\,sr^{-1}}.
\]

At

\[
T+1=724.15\ \mathrm{K},
\]

\[
L_b(724.15)
\approx
1157.7944\
\mathrm{W\,m^{-2}\,sr^{-1}}.
\]

Therefore

\[
\frac{dL_b}{dT}
\approx
\frac{
1157.7944-1150.1897
}{
2
}
\]

and

\[
\boxed{
\left.
\frac{dL_b}{dT}
\right|_{450^\circ\mathrm{C}}
\approx
3.80234\
\mathrm{W\,m^{-2}\,sr^{-1}\,K^{-1}}.
}
\]

This value is not an empirical fitting parameter.

It is the local slope of the Planck radiance integrated over the A700 spectral band.

Physically, it means that near \(450^\circ\mathrm{C}\), a 1 K change in blackbody temperature changes the ideal \(7.5\text{--}14\ \mu\mathrm{m}\) radiance by approximately

\[
3.8\
\mathrm{W\,m^{-2}\,sr^{-1}}.
\]

---

# 19. Analytical derivative of the band radiance

Define

\[
x=\frac{hc}{\lambda kT}.
\]

Planck's law can be written as

\[
B_\lambda(T)
=
\frac{2hc^2}{\lambda^5}
\frac{1}{e^x-1}.
\]

Differentiating with respect to \(T\),

\[
\boxed{
\frac{\partial B_\lambda}{\partial T}
=
\frac{2hc^2}{\lambda^5}
\frac{e^x}{(e^x-1)^2}
\frac{x}{T}.
}
\]

Since

\[
\frac{x}{T}
=
\frac{hc}{\lambda kT^2},
\]

this may also be written

\[
\boxed{
\frac{\partial B_\lambda}{\partial T}
=
\frac{2hc^2}{\lambda^5}
\frac{
e^{hc/(\lambda kT)}
}{
\left[
e^{hc/(\lambda kT)}-1
\right]^2
}
\frac{hc}{\lambda kT^2}.
}
\]

Therefore,

\[
\boxed{
\frac{dL_b}{dT}
=
\int_{7.5\mu m}^{14\mu m}
\frac{\partial B_\lambda}{\partial T}
\,d\lambda.
}
\]

Evaluating this at

\[
T=723.15\ \mathrm{K}
\]

gives the same value:

\[
\boxed{
\frac{dL_b}{dT}
\approx
3.80234\
\mathrm{W\,m^{-2}\,sr^{-1}\,K^{-1}}.
}
\]

---

# 20. Example sensitivity at \(450^\circ\mathrm{C}\), \(T_r=60^\circ\mathrm{C}\), \(\varepsilon=0.85\)

Take

\[
T=450^\circ\mathrm{C}=723.15\ \mathrm{K}
\]

and

\[
T_r=60^\circ\mathrm{C}=333.15\ \mathrm{K}.
\]

Planck integration gives

\[
\boxed{
L_b(450^\circ\mathrm{C})
=
1153.9902\
\mathrm{W\,m^{-2}\,sr^{-1}}
}
\]

and

\[
\boxed{
L_b(60^\circ\mathrm{C})
=
95.0509\
\mathrm{W\,m^{-2}\,sr^{-1}}.
}
\]

Thus

\[
L_b(T)-L_b(T_r)
=
1153.9902-95.0509
=
1058.9393.
\]

At

\[
\varepsilon=0.85
\]

and

\[
\frac{dL_b}{dT}
=
3.80234,
\]

Eq. (3) gives

\[
\frac{dT}{d\varepsilon}
=
-
\frac{
1058.9393
}{
0.85(3.80234)
}
\]

so

\[
\boxed{
\frac{dT_{\mathrm{A700}}}{d\varepsilon}
\approx
-327.6\
\mathrm{K\ per\ emissivity\ unit}.
}
\]

Therefore, locally,

\[
\boxed{
\Delta\varepsilon=0.01
\quad\Rightarrow\quad
\Delta T\approx-3.28^\circ\mathrm{C}.
}
\]

---

# 21. Exact finite-change example: true \(450^\circ\mathrm{C}\), true emissivity 0.85

Assume

\[
T_{\mathrm{true}}=450^\circ\mathrm{C},
\]

\[
\varepsilon_{\mathrm{true}}=0.85,
\]

and

\[
T_r=60^\circ\mathrm{C}.
\]

The fixed detector-band radiance is

\[
L_m
=
0.85L_b(450)
+
0.15L_b(60).
\]

Using the values above,

\[
L_m
=
0.85(1153.9902)
+
0.15(95.0509)
\]

so

\[
\boxed{
L_m
\approx
995.15\
\mathrm{W\,m^{-2}\,sr^{-1}}.
}
\]

If the camera is set correctly to

\[
\varepsilon=0.85,
\]

it returns

\[
450^\circ\mathrm{C}.
\]

If the same detector signal is interpreted using

\[
\varepsilon=0.80,
\]

the idealized band model gives

\[
\boxed{
T_{\mathrm{reported}}
\approx
467.26^\circ\mathrm{C}.
}
\]

If the same detector signal is interpreted using

\[
\varepsilon=0.90,
\]

the result is

\[
\boxed{
T_{\mathrm{reported}}
\approx
434.41^\circ\mathrm{C}.
}
\]

Thus:

| True emissivity | Entered emissivity | True temperature | Reported temperature | Error |
|---:|---:|---:|---:|---:|
| 0.85 | 0.80 | \(450^\circ\mathrm{C}\) | \(467.26^\circ\mathrm{C}\) | \(+17.26^\circ\mathrm{C}\) |
| 0.85 | 0.85 | \(450^\circ\mathrm{C}\) | \(450.00^\circ\mathrm{C}\) | \(0\) |
| 0.85 | 0.90 | \(450^\circ\mathrm{C}\) | \(434.41^\circ\mathrm{C}\) | \(-15.59^\circ\mathrm{C}\) |

For larger emissivity changes, the exact radiometric inversion should be used rather than multiplying the local derivative by \(\Delta\varepsilon\).

---

# 22. If the FLIR reads \(450^\circ\mathrm{C}\) at emissivity 0.80

Suppose the camera currently reports

\[
T_{\mathrm{FLIR}}=450^\circ\mathrm{C}
\]

with

\[
\varepsilon_{\mathrm{set}}=0.80
\]

and

\[
T_r=60^\circ\mathrm{C}.
\]

Keep the detector signal fixed and only change the emissivity setting.

The idealized A700-band results are:

| FLIR emissivity setting | Temperature from the same radiance |
|---:|---:|
| 0.80 | \(450.0^\circ\mathrm{C}\) |
| 0.85 | \(433.48^\circ\mathrm{C}\) |
| 0.88 | \(424.35^\circ\mathrm{C}\) |
| 0.90 | \(418.55^\circ\mathrm{C}\) |

Thus increasing the entered FLIR emissivity lowers the reported temperature.

---

# 23. Actual experimental comparison: FLIR 492°C at 0.80 versus COMSOL 515°C at 0.88

The actual case discussed is

\[
\boxed{
T_{\mathrm{FLIR}}=492^\circ\mathrm{C},
\qquad
\varepsilon_{\mathrm{FLIR,set}}=0.80
}
\]

and

\[
\boxed{
T_{\mathrm{COMSOL}}=515^\circ\mathrm{C},
\qquad
\varepsilon_{\mathrm{COMSOL}}=0.88.
}
\]

The raw discrepancy is

\[
\boxed{
515-492
=
23^\circ\mathrm{C}.
}
\]

These emissivity values should **not** automatically be made equal because they may represent different effective spectral quantities.

---

# 24. Reinterpreting the 492°C FLIR signal with other emissivity settings

Assume

\[
T_r=60^\circ\mathrm{C}
\]

and the existing camera reading is

\[
492^\circ\mathrm{C}
\]

with

\[
\varepsilon_{\mathrm{set}}=0.80.
\]

First recover the idealized measured radiance:

\[
L_m
=
0.80L_b(492)
+
0.20L_b(60).
\]

Planck integration gives

\[
L_b(492^\circ\mathrm{C})
=
1316.8579\
\mathrm{W\,m^{-2}\,sr^{-1}}
\]

and

\[
L_b(60^\circ\mathrm{C})
=
95.0509\
\mathrm{W\,m^{-2}\,sr^{-1}}.
\]

Therefore

\[
L_m
=
0.80(1316.8579)
+
0.20(95.0509)
\]

and

\[
\boxed{
L_m
=
1072.4965\
\mathrm{W\,m^{-2}\,sr^{-1}}.
}
\]

Keeping this measured radiance fixed gives:

| FLIR emissivity setting | Reinterpreted temperature |
|---:|---:|
| 0.80 | \(492.00^\circ\mathrm{C}\) |
| 0.85 | \(473.66^\circ\mathrm{C}\) |
| 0.88 | \(463.53^\circ\mathrm{C}\) |
| 0.90 | \(457.11^\circ\mathrm{C}\) |
| 0.95 | \(442.07^\circ\mathrm{C}\) |
| 0.96 | \(439.22^\circ\mathrm{C}\) |

Thus simply forcing FLIR to use the COMSOL value

\[
0.88
\]

would move the FLIR result **down**, not toward \(515^\circ\mathrm{C}\).

It would change

\[
492^\circ\mathrm{C}
\]

to approximately

\[
\boxed{
463.5^\circ\mathrm{C}
}
\]

under this idealized radiometric model.

Therefore making the emissivities numerically equal is not a valid way to reconcile the model and experiment.

---

# 25. Independent-temperature calibration of effective FLIR emissivity

Suppose an independent sensor gives the true surface temperature.

Examples include

- embedded thermocouple,
- calibrated contact sensor,
- instrumented puck,
- calibrated pyrometer,
- trusted reference patch.

Then the true effective A700-band emissivity can be estimated.

The measured radiance is

\[
\boxed{
L_m
=
\varepsilon_{\mathrm{set}}L(T_{\mathrm{FLIR}})
+
(1-\varepsilon_{\mathrm{set}})L(T_r).
}
\tag{6}
\]

The physical surface at independently known temperature \(T_{\mathrm{true}}\) must also satisfy

\[
\boxed{
L_m
=
\varepsilon_{\mathrm{true}}L(T_{\mathrm{true}})
+
(1-\varepsilon_{\mathrm{true}})L(T_r).
}
\tag{7}
\]

Solving Eq. (7) for emissivity,

\[
\boxed{
\varepsilon_{\mathrm{true}}
=
\frac{
L_m-L(T_r)
}{
L(T_{\mathrm{true}})-L(T_r)
}.
}
\tag{8}
\]

Substituting Eq. (6),

\[
\boxed{
\varepsilon_{\mathrm{true}}
=
\varepsilon_{\mathrm{set}}
\frac{
L(T_{\mathrm{FLIR}})-L(T_r)
}{
L(T_{\mathrm{true}})-L(T_r)
}.
}
\tag{9}
\]

This is a practical calibration equation for the effective emissivity seen by the camera.

---

# 26. Hypothetical calibration using 515°C as an independent truth point

This calculation does **not** prove that COMSOL is correct.

It asks a conditional question:

> If the real AlN temperature were independently known to be \(515^\circ\mathrm{C}\), what A700-band emissivity would be required to reproduce the observed FLIR signal of \(492^\circ\mathrm{C}\) at the current 0.80 setting?

Use

\[
T_{\mathrm{FLIR}}=492^\circ\mathrm{C},
\]

\[
\varepsilon_{\mathrm{set}}=0.80,
\]

\[
T_{\mathrm{true}}=515^\circ\mathrm{C},
\]

\[
T_r=60^\circ\mathrm{C}.
\]

The band radiances are

\[
L_b(492)
=
1316.8579,
\]

\[
L_b(515)
=
1408.5729,
\]

\[
L_b(60)
=
95.0509
\]

in units of

\[
\mathrm{W\,m^{-2}\,sr^{-1}}.
\]

Then

\[
\varepsilon_{\mathrm{true}}
=
0.80
\frac{
1316.8579-95.0509
}{
1408.5729-95.0509
}.
\]

This gives

\[
\boxed{
\varepsilon_{\mathrm{true}}
\approx
0.7441.
}
\]

Thus, **if** the actual surface were independently confirmed to be \(515^\circ\mathrm{C}\), the measured signal would correspond to an idealized A700-band effective emissivity of approximately

\[
0.744.
\]

COMSOL itself is not an independent calibration standard, so this value should not be interpreted as a measured AlN emissivity unless \(515^\circ\mathrm{C}\) is validated independently.

---

# 27. Why the FLIR and COMSOL emissivity values can affect temperature in different ways

For FLIR, at fixed measured radiance,

\[
\boxed{
\varepsilon_{\mathrm{FLIR}}\uparrow
\Rightarrow
T_{\mathrm{reported}}\downarrow.
}
\]

For COMSOL, in a hot object surrounded by cooler surfaces, increasing emissivity generally increases radiative heat loss:

\[
q_{\mathrm{rad}}
\sim
\varepsilon\sigma(T^4-T_{\mathrm{sur}}^4).
\]

Thus, for the same heating input,

\[
\boxed{
\varepsilon_{\mathrm{COMSOL}}\uparrow
\Rightarrow
\text{radiative cooling}\uparrow
\Rightarrow
T_{\mathrm{simulated}}\downarrow
}
\]

in the usual steady hot-body situation.

The important difference is:

\[
\boxed{
\text{FLIR changes temperature interpretation;}
}
\]

\[
\boxed{
\text{COMSOL changes physical heat transfer.}
}
\]

---

# 28. If COMSOL is hotter than FLIR, possible causes

If

\[
T_{\mathrm{COMSOL}}
>
T_{\mathrm{FLIR}},
\]

there are two broad possibilities.

## 28.1 COMSOL predicts a physically excessive temperature

Possible reasons include

- excessive applied heating power,
- excessive assumed absorbed heater power,
- underestimated radiative loss,
- underestimated gas conduction,
- underestimated wafer/puck-to-chuck conduction,
- underestimated thermal contact conductance,
- missing support or clamp conduction,
- missing edge losses,
- underestimated backside helium heat transfer,
- inaccurate chamber boundary temperatures,
- inadequate enclosure-radiation treatment.

## 28.2 FLIR underestimates the actual temperature

Possible reasons include

- entered emissivity higher than actual A700-band emissivity,
- incorrect reflected apparent temperature,
- uncorrected IR window transmission,
- viewport contamination,
- process-gas absorption,
- incorrect window temperature,
- directional emissivity effects,
- spectral emissivity mismatch,
- surface coating or oxidation differences.

These mechanisms must be separated before tuning the simulation.

---

# 29. Chamber reflected apparent temperature

The reflected apparent temperature used by the camera is not necessarily equal to one chamber-wall thermocouple temperature.

The puck receives irradiation from the surrounding enclosure.

In a cylindrical chamber,

\[
\text{puck}
\rightarrow
\text{walls}
\rightarrow
\text{puck}
\rightarrow
\text{camera}
\]

and multiple reflections can occur.

It is therefore conceptually better to use

\[
\boxed{
T_{\mathrm{refl,eff}}
}
\]

as the equivalent blackbody temperature representing the radiation incident on and reflected by the observed surface.

If the enclosure is nearly isothermal, diffuse, and high-emissivity, then

\[
T_{\mathrm{refl,eff}}
\approx
T_{\mathrm{wall}}
\]

may be a reasonable approximation.

For shiny, non-isothermal chamber surfaces, that approximation may be poor.

---

# 30. View angle in the top-center camera geometry

If the camera is centered above the puck, the center of the surface is viewed approximately normally:

\[
\theta\approx0.
\]

At radial position \(r\), if the camera is height \(H\) above the surface,

\[
\boxed{
\theta(r)
=
\tan^{-1}
\left(
\frac{r}{H}
\right).
}
\]

The effective emissivity can depend on angle:

\[
\varepsilon
=
\varepsilon(\lambda,T,\theta).
\]

Thus a uniform physical temperature can potentially appear nonuniform in the IR image if directional emissivity changes substantially across the field of view.

---

# 31. Viewport / IR window effects

If the A700 views the process through an IR-transparent viewport, that viewport must be included in the radiometric model.

A conceptual detector radiance is

\[
\boxed{
L_{\mathrm{det}}
=
\tau_w L_{\mathrm{scene}}
+
L_{\mathrm{window,emit}}
+
L_{\mathrm{window,refl}}
}
\]

with additional gas-transmission terms where necessary.

Relevant parameters include

- window transmission,
- window spectral transmission,
- window temperature,
- window reflection,
- window thickness,
- viewing angle,
- deposition or contamination on the window.

If the window transmission is lower than assumed, a hot target can appear cooler.

---

# 32. Process-gas effects

A gas in the optical path may

- absorb radiation,
- emit radiation,
- alter the spectral transmission between puck and camera.

Whether this matters depends on

- gas species,
- pressure,
- temperature,
- optical path length,
- spectral absorption bands.

If the chamber contains a gas that is not transparent throughout \(7.5\text{--}14\ \mu\mathrm{m}\), the simple vacuum/air radiance model is incomplete.

---

# 33. Practical AlN emissivity calibration strategy

For this application, a strong procedure is:

1. Establish a trustworthy independent temperature reference on or very near the observed AlN surface.
2. Heat the puck to a stable condition.
3. Record the A700 radiometric signal and all camera parameters.
4. Record:
   - emissivity setting,
   - reflected apparent temperature,
   - distance,
   - atmospheric temperature,
   - humidity,
   - window transmission,
   - window temperature.
5. Solve for the effective A700 emissivity using Eq. (9).
6. Repeat at several temperatures.
7. Build

\[
\boxed{
\varepsilon_{\mathrm{AlN,A700}}(T).
}
\]

A useful calibration series could span approximately

\[
300,\ 350,\ 400,\ 450,\ 500,\ 525^\circ\mathrm{C}.
\]

This produces a temperature-dependent camera emissivity rather than assuming one constant number.

---

# 34. Separate COMSOL calibration

The COMSOL emissivity should not be adjusted merely to make it equal to the camera emissivity.

For COMSOL, the desired material property is the emissivity appropriate to the modeled radiation physics.

If using a gray model, this should approximate total hemispherical emissivity.

If spectral data are available, a diffuse-spectral COMSOL formulation can instead use

\[
\varepsilon(\lambda,T)
\]

over multiple wavelength bands.

That is the more rigorous bridge between the camera and simulation:

\[
\boxed{
\text{one underlying spectral emissivity dataset}
\rightarrow
\begin{cases}
\varepsilon_{\mathrm{A700}}(T)\\
\varepsilon_{\mathrm{COMSOL}}(T)
\end{cases}
}
\]

with each effective value computed using the appropriate spectral weighting.

---

# 35. What should and should not be concluded from the current 492°C versus 515°C result

Current data:

\[
T_{\mathrm{FLIR}}=492^\circ\mathrm{C}
\]

at

\[
\varepsilon_{\mathrm{FLIR,set}}=0.80,
\]

and

\[
T_{\mathrm{COMSOL}}=515^\circ\mathrm{C}
\]

at

\[
\varepsilon_{\mathrm{COMSOL}}=0.88.
\]

What can be concluded:

- There is a \(23^\circ\mathrm{C}\) model/measurement discrepancy.
- The two emissivity numbers do not have to be identical.
- Changing the FLIR setting from 0.80 to 0.88 would lower, not raise, the FLIR temperature for the same radiometric signal.
- The A700 wavelength band can make its effective emissivity different from a total-radiation emissivity.
- AlN LWIR apparent emissivity is known experimentally to be temperature-dependent.
- A single fixed value should therefore be validated.

What cannot yet be concluded:

- that COMSOL is correct,
- that FLIR is correct,
- that the actual A700-band AlN emissivity is 0.80,
- that the actual total AlN emissivity is 0.88,
- that wavelength dependence alone explains the \(23^\circ\mathrm{C}\) difference,
- that the hypothetical \(0.744\) inverse-calculated emissivity is the real material emissivity.

The discrepancy must be resolved through independent validation and a full radiometric/thermal uncertainty analysis.

---

# 36. Key equations summary

## Planck spectral radiance

\[
\boxed{
B_\lambda(T)
=
\frac{2hc^2}{\lambda^5}
\frac{1}{
\exp\left(\frac{hc}{\lambda kT}\right)-1
}
}
\]

## Ideal A700-band radiance

\[
\boxed{
L_b(T)
=
\int_{7.5\mu m}^{14\mu m}
B_\lambda(T)\,d\lambda
}
\]

## Opaque surface radiance, simplified

\[
\boxed{
L_m
=
\varepsilon L(T)
+
(1-\varepsilon)L(T_r)
}
\]

## General temperature sensitivity to emissivity

\[
\boxed{
\frac{dT}{d\varepsilon}
=
-
\frac{
L(T)-L(T_r)
}{
\varepsilon\dfrac{dL}{dT}
}
}
\]

## Ideal A700-band sensitivity

\[
\boxed{
\frac{dT_{\mathrm{A700}}}{d\varepsilon}
=
-
\frac{
L_b(T)-L_b(T_r)
}{
\varepsilon\dfrac{dL_b}{dT}
}
}
\]

## Total-spectrum Stefan--Boltzmann sensitivity

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

## Hot-target approximation

\[
\boxed{
\frac{dT}{d\varepsilon}
\approx
-\frac{T}{4\varepsilon}
}
\]

## Effective FLIR emissivity from an independent temperature reference

\[
\boxed{
\varepsilon_{\mathrm{true}}
=
\varepsilon_{\mathrm{set}}
\frac{
L(T_{\mathrm{FLIR}})-L(T_r)
}{
L(T_{\mathrm{true}})-L(T_r)
}
}
\]

## COMSOL diffuse-gray radiosity

\[
\boxed{
J
=
(1-\varepsilon)G
+
\varepsilon e_b(T)
}
\]

with

\[
\boxed{
e_b(T)=\sigma T^4.
}
\]

## Approximate A700 effective emissivity

\[
\boxed{
\varepsilon_{\mathrm{A700}}
=
\frac{
\int R_{\mathrm{A700}}(\lambda)
\varepsilon_\lambda
B_\lambda(T)\,d\lambda
}{
\int R_{\mathrm{A700}}(\lambda)
B_\lambda(T)\,d\lambda
}
}
\]

## Total hemispherical effective emissivity

\[
\boxed{
\varepsilon_{\mathrm{total}}
=
\frac{
\int_0^\infty
\varepsilon_\lambda E_{b,\lambda}(T)\,d\lambda
}{
\sigma T^4
}
}
\]

---

# 37. Most important conceptual conclusions

1. **FLIR and COMSOL use emissivity in different calculations.**

   FLIR uses emissivity to infer temperature from measured radiation.

   COMSOL uses emissivity to calculate physical radiative heat transfer as part of the energy balance.

2. **The same underlying material can legitimately require different effective emissivities in FLIR and COMSOL.**

3. **The A700 spectral band is \(7.5\text{--}14\ \mu\mathrm{m}\).**

4. **The A700 not seeing the blackbody peak does not inherently cause it to read low.**

5. **Spectral emissivity can cause the effective A700 emissivity to differ from total emissivity.**

6. **AlN has wavelength-dependent infrared properties and experimentally observed temperature-dependent LWIR apparent emissivity.**

7. **The correct test is not to force both emissivities to the same number.**

8. **An independent temperature reference is the strongest way to determine the effective A700 emissivity of the actual AlN puck.**

9. **The \(3.80234\) band-radiance slope comes directly from differentiating/incrementing Planck radiance integrated over \(7.5\text{--}14\ \mu\mathrm{m}\) at \(450^\circ\mathrm{C}\).**

10. **For the current 492°C FLIR versus 515°C COMSOL result, emissivity, reflected radiation, viewport transmission, gas effects, and thermal-model heat losses must all be assessed before attributing the discrepancy to one cause.**

---

# 38. References

1. **FLIR A700 24° f/1.0 Professional Science Kit — official FLIR datasheet.**  
   Specifies the A700 as an uncooled microbolometer with spectral range \(7.5\text{--}14\ \mu\mathrm{m}\).  
   https://support.flir.com/dsdownload/assets/85903-0102-en-us.html

2. **FLIR A-Series Science Kits — official FLIR product documentation.**  
   Lists A700 spectral range \(7.5\text{--}14.0\ \mu\mathrm{m}\), detector type, and temperature ranges.  
   https://www.flir.com/products/a400-a700-science-kits/

3. **FLIR, The Measurement Formula / Theory of Thermography.**  
   Describes the object-emission, reflected-radiation, and atmospheric-radiation terms used in thermographic temperature measurement.  
   https://support.flir.com/DSDownload/Assets/T810442-en-US_A4.pdf

4. **COMSOL Multiphysics 6.3, The Radiosity Method for Diffuse-Gray Surfaces.**  
   Defines diffuse-gray emissivity, irradiation, radiosity, and the radiative heat-transfer source term.  
   https://doc.comsol.com/6.3/doc/com.comsol.help.heat/heat_ug_theory.07.051.html

5. **COMSOL Multiphysics 6.3, The Radiosity Method for Diffuse-Spectral Surfaces.**  
   Defines wavelength- and temperature-dependent emissivity \(\varepsilon(\lambda,T)\) and spectral bands.  
   https://doc.comsol.com/6.3/doc/com.comsol.help.heat/heat_ug_theory.07.052.html

6. **COMSOL Multiphysics 6.3, Diffuse Surface — Surface-to-Surface Radiation Interface.**  
   Documents spectral intervals, emissivity input, and the ability to define temperature-dependent emissivity.  
   https://doc.comsol.com/6.3/doc/com.comsol.help.heat/heat_ug_ht_features.09.058.html

7. **Riou, O., Logerais, P.-O., Froger, V., Durastanti, J.-F., and Bouteville, A. (2013). Thermal study of an aluminium nitride ceramic heater for spray CVD on glass substrates by quantitative thermography. Quantitative InfraRed Thermography Journal, 10(2).**  
   Reports in-situ effective-emissivity characterization of an AlN ceramic heater over approximately \(40\text{--}540^\circ\mathrm{C}\) and shows strong temperature dependence.  
   https://doi.org/10.1080/17686733.2013.793468

8. **Riou et al. (2013). Quantitative study of the temperature dependence of normal LWIR apparent emissivity. Infrared Physics & Technology, 60, 244–250.**  
   Studies the normal LWIR apparent emissivity of an AlN ceramic plate over approximately \(40\text{--}550^\circ\mathrm{C}\), connecting spectral emissivity, temperature, and camera spectral response.  
   https://doi.org/10.1016/j.infrared.2013.05.012

9. **Thickness-dependent optical properties of aluminum nitride films for mid-infrared wavelengths. Journal of Vacuum Science & Technology A.**  
   Reports AlN optical behavior and a Reststrahlen region in the mid-infrared, illustrating strong wavelength dependence of AlN dielectric properties.  
   https://doi.org/10.1116/6.0000884

---

## 39. Numerical-model caveat

The numerical values calculated in this note use:

\[
7.5\ \mu\mathrm{m}
\le
\lambda
\le
14\ \mu\mathrm{m}
\]

with an ideal flat spectral response.

They do **not** include the proprietary wavelength-dependent A700 detector/lens calibration function.

They also neglect, unless explicitly stated:

- atmospheric absorption,
- process-gas absorption/emission,
- viewport spectral transmission,
- viewport self-emission,
- multiple chamber reflections,
- directional AlN emissivity,
- non-gray chamber walls.

Therefore they should be used for physical understanding, sensitivity analysis, and first-order uncertainty estimation, rather than as an exact reproduction of FLIR firmware.

