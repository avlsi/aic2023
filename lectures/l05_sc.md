footer: Carsten Wulff 2022
slidenumbers:true
autoscale:true
theme: Plain Jane, 1
text:  Helvetica
header:  Helvetica

<!--pan_skip: -->

## TFE4188 - Lecture 5 introduction
# Switched-Capacitor Circuits

---


# Goal

Understand **why** would use switched capacitor circuits

Introduction to **discrete-time**, and **switched capacitor** and the **circuits** we need

---

<!--pan_skip: -->

#[fit] Why

---

# Active-RC

<!--pan_doc: 

A general purpose Active-RC bi-quadratic (two-quadratic equations) filter is shown below 

-->

![left fit](../media/l4_activebiquad.pdf)

<!--pan_doc:

If you want to spend a bit of time, then try and calculate the transfer function below.

-->


$$H(s) = \frac{\left[ \frac{C_1}{C_B}s^2 + \frac{G_2}{C_B}s + (\frac{G_1G_3}{C_A C_B})\right]}{\left[ s^2  + \frac{G_5}{C_B}s + \frac{G_3 G_4}{C_A C_B}\right]}$$


<!--pan_doc: 

Active resistor capacitor filters are made with OTAs (high output impedance) or OPAMP
(low output impedance). Active amplifiers will consume current, and in Active-RC 
the amplifiers are always on, so there is no opportunity to reduce the current
consumption by duty-cycling (turning on and off).

Both resistors and capacitors vary on an integrated circuit, and the
3-sigma variation can easily be 20 %. 

The pole or zero frequency of an Active-RC filter is proportional to the inverse of the 
product between R and C
-->

$$\omega_{p|z} \propto \frac{1}{RC}$$

<!--pan_doc:

As a result, the total variation of the pole or zero frequency is can have a 
3-sigma value of 

$$ \sigma_{RC} = \sqrt{( \sigma_R_^2 + \sigma_C_^2)} = \sqrt{0.02^2 + 0.02^2} = 0.028 = 28 \%$$

On an industrial IC we sometimes need to calibrate the R or C in production 
to get an accurate RC time constant.

We cannot physically change an IC, every single one of the 100 million copies
of an IC is from the same Mask set. That's why ICs are cheap. To make the Mask set is 
incredibility expensive (think 5 million dollars), but a copy made from the Mask set can cost one dollar or less. To calibrate we need additional circuits.

Imagine we need a resistor of 1 kOhm. We could create that by parallel connection
of larger resistors, or series connection of smaller resistors. Since we know the maximum
variation is 0.02, then we need to be able to calibrate away +- 20 Ohms. We could have
a 980 kOhm resistor, and then add ten 4 Ohm resistors in series that we can short 
with a transistor switch. 

But is a resolution of 4 Ohms accurate enough? What if we need a precision of 0.1%?
Then we would need to tune the resistor within +-1 Ohm, so we might need 80 0.5 Ohm
resistors. 

But how large is the on-resistance of the transistor switch? Would that 
also affect our precision? 

But is the calibration step linear when we add the transistors. If we have a non-linear 
calibration step, then we cannot use gradient decent calibration algorithms, nor can we
use binary search. 

Analog designers need to deal with an almost infinite series of "But", and 
the experienced designer will know when to stop, when is the "But what if" not 
a problem anymore. 

The most common error in analog integrated circuit design is a "I did not imagine that 
my circuit could fail in this manner" type of problem. Or, not following the line of "But"'s far enough.

But if we follow all the "But"'s we will never tapeout!

Although active-RC filters are great for linearity and easy to drive, but if we need accurate time constant, there are better alternatives.

-->

---


# Gm-C


![fit right](../media/l4_gmcbi.pdf)

$$ H(s) = \frac{ s^2\frac{C_X}{C_X + C_B} + s\frac{G_{m5}}{C_X + C_B} + \frac{G_{m2}G_{m4}}{C_A(C_X + C_B)}}
{s^2 + s\frac{G_{m2}}{C_X + C_B} + \frac{G_{m1}G_{m2}}{C_A(C_X + C_B)} }$$

<!--pan_doc:

The pole and zero frequency of a Gm-C filter is 

-->


$$\omega_{p|z} \propto \frac{G_m}{C}$$

<!--pan_doc:

The transconductance accuracy depends on the circuit, and the bias circuit, so we can't give a general, applies for all circuits, sigma number. 
Capacitors do have 3-sigma 20 % variation, usually.

Same as Active-RC, Gm-C need calibration to get accurate pole or zero frequency.

-->

---

# Switched capacitor circuits

<!--pan_doc:

We can make switches and capacitors behave as a resistor.

-->

## $$\omega_{p|z} \propto \frac{C_1}{C_2}$$

<!--pan_doc:

These switched capacitor "resistors" can be used in circuits similar to Active-RC filters, 
and it's possible to get a pole or zero frequency proportional to a the relative size of capacitors. 

This is a fantastic feature, if we make two identical capacitors in our layout, we won't know the absolute size, whether the $C_1$ is 100 fF or 80 fF, but 
we can be certain that if $C_1 = 80$ fF, then $C_2 = 80$ fF to a precision of possibly 0.1 %. 

With switched capacitor filters we can accurately set the pole and zero frequency accurately (as long as we have an accurate clock).

SC circuits can achieve high linearity, accurate DC gain, accurate poles, accurate zeros. 

They do have a drawback that they are discrete time circuits, as such, we must treat them with caution, and they will always need 
some analog filter before to avoid a phenomena we call aliasing. 

-->


## Accurate gain 

## Common mode rejection

[A pipelined 5-Msample/s 9-bit analog-to-digital converter](https://ieeexplore.ieee.org/document/1052843)


![right fit](../media/lewis.png)

---

# Discrete-Time Signals
---

[.column]

Define $$ x_c$$ as a continuous time signal, continuous value signal

Define $$ 
\ell(t) = \begin{cases}
1 & \text{if } t \geq 0 \\
0 & \text{if } t < 0
\end{cases}
$$

Define $$ x_{sn}(t) = \frac{x_c(nT)}{\tau}[\ell(t-nT) - \ell(t - nT - \tau)]$$

Define $$ x_s(t) = \sum_{n=-\infty}^{\infty}{x_{sn}(t)}$$

[.column]

A sampled signal of an analog signal you can think of it as an infinite sum of pulse trains where the area under the pulse train is equal to the analog signal. 

__Why do this?__

---

[.column]

If $$ x_s(t) = \sum_{n=-\infty}^{\infty}{x_{sn}(t)}$$

Then $$ X_{sn}(s) = \frac{1}{\tau}\frac{1 - e^{-s\tau}}{s} x_c(nT)e^{-snT} $$

And  $$ X_s(s) = \frac{1}{\tau}\frac{1 - e^{-s\tau}}{s} \sum_{n=-\infty}^{\infty}x_c(nT)e^{-snT}$$

Thus $$ \lim_{\tau \to 0} \rightarrow X_s(s) = \sum_{n=-\infty}^{\infty}x_c(nT)e^{-snT}$$

Or $$ X_s(j\omega) = \frac{1}{T}\sum_{k=-\infty}^{\infty} X_c\left(j\omega - \frac{j k 2 \pi}{T}\right)$$

[.column]

**The spectrum of a sampled signal is an infinite sum of frequency shifted spectra**

or equivalenty

**When you sample a signal, then there will be copies of the input spectrum at every $$ nf_s$$**

However, if you do an FFT of a sampled signal, then all those infinite spectra will fold down between $$ 0 \to f_s/2$$ or $$- f_s/2 \to f_s/2$$ for a complex FFT

---

![fit](../media/l5_sh.pdf)

---

![fit](../media/l5_shaaf.pdf)

---

![fit](../media/l5_subsample.pdf)

---

[.column]

$$ X_s(s) = \sum_{n=-\infty}^{\infty}x_c(nT)e^{-snT}$$
$$ X_s(z) = \sum_{n=-\infty}^{\infty}x_c[n]z^{-n}$$

[.column]


For discrete time signal processing we use Z-transform

If you're unfamiliar with the Z-transform, read the book or search [https://en.wikipedia.org/wiki/Z-transform](https://en.wikipedia.org/wiki/Z-transform)

---

![left fit](../media/l5_sdomain.pdf)

#[fit] Pole-Zero plots

If you're not comfortable with pole/zero plots, have a look at 

[What does the Laplace Transform really tell us](https://www.youtube.com/watch?v=n2y7n6jw5d0)

---
#[fit] Z-domain

Spectra repeat every $$2\pi$$ (every $$f_s$$)

Bi-linear transform 

$$ s = \frac{z -1}{z + 1}$$

<sub>Warning: First-order approximation [https://en.wikipedia.org/wiki/Bilinear_transform](https://en.wikipedia.org/wiki/Bilinear_transform)</sub>


![left fit](../media/l5_zdomain.pdf)

---
# First order filter 

$$ y[n+1] = bx[n] + ay[n] \Rightarrow y z = b x + a y$$

$$ y[n] = b x[n-1] + ay[n-1] \Rightarrow y = b x z^{-1} + a y z^{-1}  $$ 

$$ H(z) = \frac{b}{z-a}$$


Infinite-impulse response (IIR)

$$ h[n] = \begin{cases} k & \text{if } n < 1 \\ a^{n-1}b + a^n k & \text{if } n \geq 1 \end{cases}$$


<sub> Head's up: Fig 13.12 in AIC is wrong </sub>

![left fit](../media/l5_zunstable.pdf)


---

![left fit](../media/l5_fir.pdf)

# Finite-impulse response(FIR)

$$ H(z) = \frac{1}{3}\sum_{i=0}^2 z^{-1}$$



---

#[fit] Switched-Capacitor

---

## **Q:** How do switched capacitor circuits work?

---

![original fit](../media/l5_scintro.pdf)

---

#[fit] Principles


---

## Switched capacitor gain circuit

---

![left fit](../media/l5_scamp.pdf)

![right fit](../media/l5_scfig.pdf)

---

![left fit](../media/l5_scamp.pdf)

$$ V_o[n+1] = \frac{C_1}{C_2}V_i[n]$$

$$ V_o z = \frac{C_1}{C_2} V_i$$

$$ \frac{V_o}{V_i} = H(z) = \frac{C_1}{C_2}z^{-1}$$

---

## Switched capacitor integrator 

---

![left fit](../media/l5_scint.pdf)

![right fit](../media/l5_scifig.pdf)

---

![left fit](../media/l5_scint.pdf)

$$ V_o[n] = V_o[n-1] + \frac{C_1}{C_2}V_i[n-1]$$

$$V_o - z^{-1}V_o = \frac{C_1}{C_2}z^{-1}V_i $$

$$ H(z) = \frac{C_1}{C_2}\frac{z^{-1}}{z^{-1} + 1} =
\frac{C_1}{C_2}\frac{1}{z-1} $$

---

![left fit](../media/l5_pzz.pdf)


#[fit] $$ H(z) =\frac{C_1}{C_2}\frac{1}{z-1} $$

---

#[fit] Noise

---

![original fit](../media/l5_noise.pdf)

---

#[fit] Both phases add noise, $$ V_n^2 > \frac{2 k T}{C}$$

---
[.background-color: #000000]
[.text: #FFFFFF]

[.column]

[Mean](https://en.wikipedia.org/wiki/Mean)
$$ \overline{x(t)} = \lim_{T\to\infty} \frac{1}{T}\int^{+T/2}_{-T/2}{ x(t) dt} $$

Mean Square
$$ \overline{x^2(t)} = \lim_{T\to\infty} \frac{1}{T}\int^{+T/2}_{-T/2}{ x^2(t) dt} $$

[Variance](https://en.wikipedia.org/wiki/Variance)
$$ \sigma^2 = \overline{x^2(t)} - \overline{x(t)}^2$$

where $$\sigma$$ is the standard deviation.
If mean is removed, or is zero, then
$$ \sigma^2 = \overline{x^2(t)} $$

[.column]
Assume two random processes, $$x_1(t)$$ and $$x_2(t)$$ with mean of zero (or removed).
 $$ x_{tot}(t) =  x_1(t) + x_2(t)$$
 $$ x_{tot}^2(t) = x_1^2(t) + x_2^2(t) + 2x_1(t)x_2(t)$$

Variance (assuming mean of zero) 
$$ \sigma^2_{tot} = \lim_{T\to\infty} \frac{1}{T}\int^{+T/2}_{-T/2}{ x_{tot}^2(t) dt} $$
$$ \sigma^2_{tot} = \sigma_1^2 + \sigma_2^2 + \lim_{T\to\infty} \frac{1}{T}\int^{+T/2}_{-T/2}{ 2x_1(t)x_2(t) dt} $$

**Assuming uncorrelated processes (covariance is zero), then
$$ \sigma^2_{tot} = \sigma_1^2 + \sigma_2^2  $$**

---

## Sub-circuits for SC-circuits

---

#[fit] OTA

---

## **Q:** How many different OTAs do you know?

---

![fit](../media/diff_ota.png)

---

![fit](../media/diff_ota_bias.png)



---

#[fit] Switches

---

![original fit](../media/l5_sw1.pdf)

---

![original fit](../media/l5_sw2.pdf)

---

![original fit](../media/l5_sw3.pdf)

---

#[fit] Non-overlapping clocks

---

![original fit](../media/l5_novl.pdf)

---


#[fit] Example

---

![fit](../media/l5_scex.pdf)

---

# In the book that you really should read

Parasitic sensitive integrator

Gain circuit that zero's OTA offset

Correlated double sampling

VCO

Peak detectors

---


#[fit] Thanks!

---