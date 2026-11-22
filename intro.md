# Introduction

This supplement is about **how a model is specified in ModelFlow** — the step that turns
a piece of readable text into a model object that can be simulated on a Pandas DataFrame.

A ModelFlow model is written in a small domain specific language (DSL). Each equation
states a piece of business logic, one endogenous variable per equation:

```text
> profit__{banks} = revenue__{banks} - expenses__{banks}
```

Before such text can be solved, it has to be **expanded** (loops over lists, sums, tags),
**normalized** (so the endogenous variable stands alone on the left-hand side) and
optionally extended with **add factors**. `Makemodel` performs the expansion,
`normal` performs the normalization, and the `%%Makemymodel` cell magic lets you do the
whole thing in a notebook cell.

Where the DSL comes from and how it becomes executable code is described in the
architecture supplement, *ModelFlow - Architecture and History*, under
[The ModelFlow equation DSL](https://ibhansen.github.io/general/modelflow-specification-to-code/#the-modelflow-equation-dsl)
and [Higher-level model specification DSL](https://ibhansen.github.io/general/modelflow-specification-to-code/#higher-level-model-specification-dsl).

## What is in this book

**[Model specification](model%20specification.ipynb)** introduces the DSL and the
`Makemodel` preprocessor: list definitions and `do`-loops, `doable`, `sum()`, the
`<exo>`, `<add>` and `<fit>` tags, `replacements=` and `list_defs=`, and how several
model blocks are combined with `+` into a `Listmodels` container.

**[The `%%Makemymodel` magic](jupyter_magic_makemymodel.ipynb)** shows the notebook
front end: writing Markdown documentation and `>` equations in the same cell, the
available options (`show`, `draw`, `render`, `latex`), and how `segment=` lets you build
a large model from independently developed pieces.

**[Input formats](input%20formats.ipynb)** is about the text `Makemodel` is given. Markdown is
the main road, but `type_input=` also reads plain ModelFlow text (with or without
`FRML ... $`), Gekko and EViews equations, and with `eq_start=`, `eq_end=`,
`comment_start=` and `transform=` you describe a format of your own: where an equation
starts and ends, what a comment is, and a function which translates each equation.

**[Estimation](estimation.ipynb)** covers the behavioural equations, whose coefficients
come from data rather than from accounting: the equation syntax for coefficients and
constraints, the backends (`ols`, `nls_lmfit`, EViews, and R through `r_lm` and `r_nls`),
the fit of the variable itself when an equation is estimated in growth rates, and how an
equation tagged `<estimator=...>` is estimated while the model is being built. It stays
with the syntax, demonstrated on small synthetic data where the right answer is known in
advance; a complete estimation on real data is the
[Estimation example](estimation%20example.ipynb) chapter.

**[Variables at a fixed period](fixed%20periods.ipynb)** covers `X(2015)` -- the value of `X`
in one particular period, as in an index with 2015 = 100 -- and `X('2015Q1')` for quarterly
and monthly data: the syntax, how a model evaluates it, which solvers can solve an
endogenous variable at a fixed period inside the solve span, and how it works in estimation.

**[Normalization](normal.ipynb)** goes one level down, to the `normal` function that
rewrites an equation into `y = F(y, x)` form, and to the `Normalized_frml` class that
carries the original, preprocessed, normalized and fitted versions of an equation.

**[Add factors](add%20factors.ipynb)** looks in detail at what the `<add>` tag actually
generates, and how the resulting add factor can be used to control an equation.

**[Estimation example](estimation%20example.ipynb)** puts it all together on real data: national
accounts for Nepal, behavioural equations estimated one at a time and then while the model
is built, add factors that make the model reproduce history and the forecast that comes
with the data, and an experiment run against the resulting baseline. It is written by
Andrew Burns and Ib Hansen.

**[Differential equations](differential%20equations.ipynb)** is about models written in
continuous time, $\frac{dS}{dt} = f(S, x)$: an epidemic, a projectile, planets. The derivatives
are tagged `<ODE>`, and `modelode` generates the equations of a Runge–Kutta method (Euler, Heun,
RK4), one DataFrame row per time step. It covers what is generated, the choice of method and
time step, documented models with lists, where `NO{list}` means all the other elements, and
stiff systems solved with implicit methods and Newton. The feature is experimental: check the
results more carefully than usual.

## Prerequisites

The notebooks import from the ModelFlow source, mainly:

```python
from modelconstruct_estimation import Makemodel
import modeljupytermagic          # registers the %%Makemymodel magic
from modelnormalize import normal
```

Estimation needs no import of its own: the method is named in the equation's tag,
as `<est=ols>` or `<est=nls_lmfit>`. The R backends need R itself, installed from
[CRAN](https://cran.r-project.org); see the [Estimation](estimation.ipynb) chapter.

Each notebook can be read on its own, but the chapters are ordered so that later ones
build on the vocabulary of the earlier ones.
