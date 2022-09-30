# CapacitorCalculationsExtensionModule
 Extension module for python to calculate interdigital capacitor related things

# Install
In a terminal type:

`pip install [path to git repos]\CapacitorCalculations\CapacitorCalculations`

If there is a permission error add the `--user` tag to the end of the command.

# Documentation
This package includes functions which make calculating the capacitance for interdigital capacitors possible.

#### References

(1) Huey-Daw Wu, Zhihang Zhang, F. Barnes, C. M. Jackson, A. Kain and J. D. Cuchiaro, "Voltage tunable capacitors using high temperature superconductors and ferroelectrics," in IEEE Transactions on Applied Superconductivity, vol. 4, no. 3, pp. 156-160, Sept. 1994, doi: 10.1109/77.317831.

(2) S. S. Bedair and I. Wolff, "Fast, accurate and simple approximate analytic formulas for calculating the parameters of supported coplanar waveguides for (M)MIC's," in IEEE Transactions on Microwave Theory and Techniques, vol. 40, no. 1, pp. 41-48, Jan. 1992, doi: 10.1109/22.108321.

### Functions
`ellint_ratio(k: float) -> float`

This is used to calculate $\frac{K(k)}{K'(k)}$ where $K$ is the complete elliptic integral of the first kind and $K'$ is its complement such that $K'(k)=K(k')$ and $k'=\sqrt{1-k^2}$. This uses the `comp_ellint1` from the cmath standard library. $K(0)=\frac{\pi}{2}$, but $\lim\limits_{k\rightarrow 1}K(k)\rightarrow \tilde{\infty}$, so for small $k$, $K'(k)$ becomes problematic. This starts to happen for $k < 10^{-7}$. For these cases of small $k$, you should use `ellint_ratio_aprrox`.

`k`: modulus $k$ for complete elliptic integral of the first kind.

return: $\frac{K(k)}{K'(k)}$

---

`ellint_ratio_approx(k: float) -> float`

This is used to calculate $\frac{K(k)}{K'(k)}$ for small $k$ $(k < 0.05)$. This is based on the approximation in ref. (2).

$\frac{K(k)}{K'(k)}=\pi/\ln[2\frac{1+(1-k^2)^{1/4}}{1-(1-k^2)^{1/4}}]$ for $0 < k < 0.25$

However, for very small $k$, the 4th root part can lead to computational issues, so this function uses an 8th order Taylor expansion of $(1-k^2)^{1/4}$ to ease this burden. This reduces the valid range to $0 < k < 0.05$ which is perfect for covering the limitations of the previous function.

`k`: modulus $k$ for complete elliptic integral of the first kind.

return: small $k$ approximation for $\frac{K(k)}{K'(k)}

---

`k_thin(g: float, h: float, u: float) -> float`

This is used to calculate the modulus $k$ for the complete elliptic integral of the first kind $K(k)$. The modulus $k$ is for a particular material, such as air, the substrate, or a material grown on the substrate. All three values `g`, `h`, and `u` must be in the same length units, but this function doesn't care what units you use.

`g`: the "gap distance"; i.e. the spacing between fingers.

`h`: the height; i.e. the thickness of the material that is contributing to the function.

`u`: the unit cell size; i.e. the spacing between fingers + the width of a finger.

return: modulus $k$

---

`k_thick(g: float, u: float) -> float`

This is used to calculate the modulus $k$ for the complete elliptic integral of the first kind $K(k)$ for a material that has a thickness large enough to approach the limiting function. As long as $h \gg u$, this is valid. This function is typically used for finding $k_{\text{air}}$. `g` and `u` must have the same length units.

`g`: the "gap distance"; i.e. the spacing between fingers.

`u`: the unit cell size; i.e. the spacing between fingers + the width of a finger.

return: modulus $k$.

---

`capacitance_bare(g: float, u: float, h_s: float, N: int, l: float, eps_s: float) -> float`

This calculates the capacitance of an interdigital capacitor on a substrate of a single material based on the geometry of the capacitor and the substrate. `g`, `u`, and `h_s` must have the same length units, while `l` must be in mm. 

`g`: the "gap distance"; i.e. the spacing between fingers.

`u`: the unit cell size; i.e. the spacing between fingers + the width of a finger.

`h_s`: the thickness of the substrate.

`N`: total number of fingers on the capacitor.

`l`: length of a finger in mm.

`eps_s`: relative dielectric constant $\vareps_r$ of the substrate.

return: capacitance in pF

---

`capacitance_bare_k(k_a: float, k_s: float, N: int, l: float, eps_s: float) -> float`

This calculates the capacitance of an interdigital capacitor on a substrate of a single material based on the modulii $k$. `l` must be in mm. 

`k_a`: modulus for air, calculated with `k_thick()`

`k_s`: modulus for the substrate, calculated with `k_thin()`

`N`: total number of fingers on the capacitor.

`l`: length of a finger in mm.

`eps_s`: relative dielectric constant $\vareps_r$ of the substrate.

return: capacitance in pF

---

capacitance_geometric(g: float, h_f: float, u: float, N: int, l: float) -> float

Calculates a capacitance that is related to the geometry of the film. This aids in analyzing the films dielectric constant. `g`, `h_f`, and `u` must have the same units.

`g`: the "gap distance"; i.e. the spacing between fingers.

`h_f`: the thickness of a thin film.

`u`: the unit cell size; i.e. the spacing between fingers + the width of a finger.

`N`: total number of fingers on the capacitor.

`l`: length of a finger in mm.

return: capacitance in pF.

---

`capacitance_geometric_k(k_f: float, N: int, l: float) -> float`

Calculates the same as `capacitance_geometric()` but takes $k_{\text{film}}$ as an argument instead of the geometric factors.

`k_f`: modulus $k$ for the film.

`N`: total number of fingers on the capacitor.

`l`: length of a finger in mm.

return: capacitance in pF.

---

`capacitance_total(g: float, h_f: float, eps_f: float, u: float, h_s: float, N: int, l: float, eps_s: float) -> float`

Calculates the capacitance of an interdigtal capacitor on a substrate with a well characterized film grown on it. `g`, `h_f`, `u`, and `h_s` must all be in the same length units.

`g`: the "gap distance"; i.e. the spacing between fingers.

`h_f`: the thickness of a thin film.

`eps_f`: the dielectric constant of the thin film.

`u`: the unit cell size; i.e. the spacing between fingers + the width of a finger.

`h_s`: the thickness of the substrate.

`N`: total number of fingers on the capacitor.

`l`: length of a finger in mm.

`eps_s`: the dielectric constant of the substrate.

return: capacitance in pF.

---

`capacitance_total_k(g: float, h_f: float, eps_f: float, u: float, h_s: float, N: int, l: float, eps_s: float) -> float`

Calculates the capacitance of an interdigtal capacitor on a substrate with a well characterized film grown on it. This is the same as `capacitance_total()`, except it uses the modulii instead of the geometric factors.

`k_a`: $k_{\text{air}}$.

`k_s`: $k_{\text{substrate}}$.

`k_f`: $k_{\text{film}}$.

`eps_f`: the dielectric constant of the thin film.

`N`: total number of fingers on the capacitor.

`l`: length of a finger in mm.

`eps_s`: the dielectric constant of the substrate.

return: capacitance in pF.

---

dielectric_constant_relative(C_t: float, C_0: float, g: float, h_f: float, u: float, N: int, l: float) -> float

Calculate the dielectric constant of a thin film grown on an interdigital capacitor.

`C_t`: measured total capacitance.

`C_0`: measured capacitance before film was grown.

`g`: the "gap distance"; i.e. the spacing between fingers.

`h_f`: the thickness of a thin film.

`u`: the unit cell size; i.e. the spacing between fingers + the width of a finger.

`N`: total number of fingers on the capacitor.

`l`: length of a finger in mm.

return: $\vareps_{\text{film}}$

---

dielectric_constant_relative_k(C_t: float, C_0: float, k_f: float, N: int, l: float) -> float

Calculate the dielectric constant of a thin film grown on an interdigital capacitor.

`C_t`: measured total capacitance.

`C_0`: measured capacitance before film was grown.

`k_f`: modulus $k$ for the film.

`N`: total number of fingers on the capacitor.

`l`: length of a finger in mm.

return: $\vareps_{\text{film}}$
