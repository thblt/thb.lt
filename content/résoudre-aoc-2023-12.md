---
title: Solving “Hot Springs” (Advent of Code 2023, day 12)
layout: post
---

## Introduction

In typical AoC fashion, the second part is the same problem as the
first's, but with significantly increased complexity.  The way that
day increases complexity is by multiplying each constraint and pattern
five times, by inserting a question mark `?` between repetitions of
the pattern.  So for part 2, this input:


```
##.?# 1, 2
```

becomes:

```
##.?#?##.?#?##.?#?##.?#?##.?# 1,2,1,2,1,2,1,2,1,2
```

What's worth noticing there is that this way of making the input more
complex works by *repeating* the same problem.  This hints towards the
solution:

 - d'un côté, on va éviter de résoudre les problèmes d'un bloc, mais
   plutôt les fragmenter en plusieurs plus petits problèmes, qu'on va
   résoudre séparément (la solution d'un problème est simplement le
   produit des solutions de ses sous-problèmes)
 - on va compter sur le fait qu'en fragmentant, on va voir revenir
   plusieurs fois les mêmes sous-problèmes.  Comme c'est idiot de
   recalculer plusieurs fois la même chose, on va garder en cache tous
   les résultats déjà obtenus.

## Solution générale

Je définis le solveur comme un tableau d'association (une `HashMap`,
en Python on parle de dictionnaires) qui associe des problèmes à des
solutions:

```rust
struct Solver (HashMap<(Constraint, Pattern), usize>);
```

Ce solveur a un point d'entrée unique, la fonction `solve(Constraint,
Pattern) -> usize`.  La première chose que fait `solve` est de choisir
une stratégie parmi 4, dans cet ordre de priorité (les critères sont
cumulatifs: la première stratégie correspondante est appelée):

Ces stratégies peuvent faire deux choses: soit résoudre directement le
problème, soit le découper en deux sous-problèmes et les repasser à `solve()`.

  1. If there are **no** `?` in the constraint, we can't go further, so we
     just need to → **Verify!**.
  3. If there are **only** `?`s in the constraint (no `#` and `.`), we
     can fully ignore it (except for its length) and just compute the number
     of solutions from the pattern → **Trivial**
  1. If there's a `.` in the constraint, we can **Split!** it in two,
     smaller, constraint.
  4. Otherwise, → **Aggressive!**

### La stratégie «Découpe»

Si l'entrée contient au moins un `.`, on**Découpe**, sinon on passe à
la suivante

, c'est-à-dire qu'on divise la contrainte en deux sous-contraintes,
une à gauche, une à droite du `.`, en supprimant celui-ci.  Ensuite,
on découpe de la même façon le motif de toutes les façons compatibles
avec la longueur de chacune des contraintes.  Par exemple:

```
    ┌ Split point
????.???#.??? 1, 2, 4
├──┘ └──────┤
└ Left part └ Right part
```

On découpe d'abord en une partie gauche, `????`, et une partie
droite, `???#.???`.

```
result = 0
[1,2,4] []: découpage impossible, la partie gauche est trop
courte.
[1,2] [4]: découpage possible, on ajoute au résultat:
result += solve("????", [1,2]) * solve("???#.???", [4])
[1] [2,4]: découpage possible, on ajoute au résultat:
result += solve("????", [1]) * solve("???#.???", [2,4])
(Pourquoi possible? Parce qu'on teste simplement la longueur des
contraintes, et [2,4] rentre bien dans la longueur de la
partie droite.  Elle sera éliminée à l'étape suivante).
[] [1,2,4]: découpage impossible, la partie droite est trop
courte.
```

### Stratégie «vérification»

2. Si l'entrée ne contient aucun `?`, on **Vérifie**.  Si la solution
est valide, on renvoie 1, zéro sinon.

### La stratégie triviale

Dans le cas particulier où la contrainte ne contraint rien,
c'est-à-dire qu'elle ne contient que des `?`, et aucun `#` ni `.`, le
nombre de possibilités se calcule par la formule:

### Stratégie par défaut

S'applique au cas où l'entrée contient des `#` et des `?`, après que
les `.` ont été découpés en sous-entrées.  Cette stratégie procède
aussi par découpage: elle cherche toutes les façons possibles de
positionner le premier élément du motif, de manière à:

 - ne pas avoir de `#` supplémentaire à gauche (puisqu'on place le
   premier élément).
 - si le motif contient plus d'un élément, ajouter un `.`
   immédiatement à sa droite.

Pour chacune de ces possibilités, elle appelle à nouveau `solve()` avec
la partie droite de la contrainte et le reste du motif, et renvoie
la somme des solutions.

## Remarques

Pour l'essentiel, on voit une boucle: on entre par `solve()`, qui à
son tour appelle découpe et agressive, qui de deux façons différentes
divisent le problème en deux sous-problèmes, qu'ils passent en retour
à `solve()`, qui choisit une stratégie, etc.  Ce que l'on gagne à
toujours revenir à solve()=, c'est la mémoïsation: la forme générale
de solve est:

```rust
impl Solver {
    pub fn solve(&mut self, constraint: &[State], pattern: &[usize]) -> usize {
        let key = (constraint.to_vec(), pattern.to_vec());
        if let Some(result) = self.memory.get(&key) {
            *result
        } else {
            let result = self.do_solve(constraint, pattern);
            self.memory.insert(key, result);
            result
        }
    }
}
```
