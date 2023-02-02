# Funções de alta ordem em haskell

Funções de Alta Ordem são funções que possuem, como argumentos, outras funções ou retornam funções. Isso permite que elas possam ser aplicadas e combinadas de diversas formas, aumentando a flexibilidade e reusabilidade do código. Em Haskell, as funções de alta ordem são amplamente utilizadas, já que a linguagem é fortemente influenciada pela programação funcional.

A seguir, serão apresentadas algumas funções de alta ordem comuns em Haskell, explicando o que cada uma delas faz e como elas podem ser úteis na prática:

## Funções

# span: 

span :: (a -> Bool) -> [a] -> ([a], [a]) - Essa função leva um predicado (a -> Bool) e uma lista [a], e retorna um par de listas, ([a], [a]), onde a primeira lista consiste em todos os elementos da lista original antes da primeira ocorrência de um elemento que não satisfazem a condição, e a segunda lista consiste nos elementos restantes. Exemplo de uso:

```haskell
span                    :: (a -> Bool) -> [a] -> ([a],[a])
span _ xs@[ ]            =  (xs, xs)
span p xs@(x:xs')
         | p x          =  let (ys,zs) = span p xs' in (x:ys,zs)
         | otherwise    =  ([ ],xs)
```

```
span (< 5) [1, 2, 3, 4, 5, 6, 7]
-- Resultado: ([1, 2, 3, 4], [5, 6, 7])
```

# takeWhile: 

takeWhile :: (a -> Bool) -> [a] -> [a] - Essa função leva um predicado (a -> Bool) e uma lista [a], e retorna uma lista consistindo do prefixo mais longo de elementos da lista original que satisfazem o predicado. Exemplo de uso:

```haskell
takeWhile               :: (a -> Bool) -> [a] -> [a]
takeWhile _ []          =  []
takeWhile p (x:xs)
            | p x       =  x : takeWhile p xs
            | otherwise =  []
```

```
takeWhile (< 5) [1, 2, 3, 4, 5, 6, 7]
-- Resultado: [1, 2, 3, 4]
```

# dropWhile: 

O que seria o predicado: takeWhile :: (a -> Bool) -> [a] -> [a] - Essa função leva um predicado (a -> Bool) e uma lista [a], e retorna uma lista consistindo do prefixo mais longo de elementos da lista original que não satisfazem o predicado. Exemplo de uso:

```haskell
dropWhile               :: (a -> Bool) -> [a] -> [a]
dropWhile _ []          =  []
dropWhile p xs@(x:xs')
            | p x       =  dropWhile p xs'
            | otherwise =  xs
```

```
dropWhile (< 5) [1, 2, 3, 4, 5, 6, 7]
-- Resultado: [5, 6, 7]
```

# iterate: 

iterate :: (a -> a) -> a -> [a] - Essa função leva uma função (a -> a) e um valor do tipo a, e retorna uma lista infinita de aplicações repetidas da função ao valor. Exemplo de uso:

```haskell
iterate :: (a -> a) -> a -> [a]
iterate f x =  x : iterate f (f x)
```

```
take 5 (iterate (*2) 1)
-- Resultado: [1, 2, 4, 8, 16]
```

# scanr:

scanr :: (a -> b -> b) -> b -> [a] -> [b] - Essa função leva uma função binária (a -> b -> b), um valor inicial do tipo b, e uma lista [a], e retorna uma lista de resultados intermediários obtidos pela dobragem da lista da direita com a função binária e o valor inicial. Exemplo de uso:

```haskell
scanr                   :: (a -> b -> b) -> b -> [a] -> [b]
scanr _ q0 []           =  [q0]
scanr f q0 (x:xs)       =  f x q : qs
                           where qs@(q:_) = scanr f q0 xs
```

```
scanr (+) 0 [1, 2, 3, 4]
-- Resultado: [10, 9, 7, 4, 0]
```

# scanl: 

scanl :: (b -> a -> b) -> b -> [a] -> [b] - Essa função leva uma função binária (b -> a -> b), um valor inicial do tipo b, e uma lista [a], e retorna uma lista de resultados intermediários obtidos pela dobragem da lista da esquerda com a função binária e o valor inicial. Exemplo de uso:

```haskell
scanr1                  :: (a -> a -> a) -> [a] -> [a]
scanr1 _ []             =  []
scanr1 _ [x]            =  [x]
scanr1 f (x:xs)         =  f x q : qs
                           where qs@(q:_) = scanr1 f xs
```

```
scanl (+) 0 [1, 2, 3, 4]
-- Resultado: [0, 1, 3, 6, 10]
```

# unfoldr: 

unfoldr :: (b -> Maybe (a, b)) -> b -> [a] - Essa função recebe uma função (b -> Maybe (a, b)) e um elemento inicial b, e retorna uma lista com os resultados da aplicação sucessiva da função ao elemento inicial. A função deve retornar Nothing quando não houver mais elementos para serem adicionados à lista. Exemplo de uso:

```haskell
unfoldr f b0 = build (\c n ->
  let go b = case f b of
               Just (a, new_b) -> a `c` go new_b
               Nothing         -> n
  in go b0)
```

```
unfoldr (\b -> if b == 0 then Nothing else Just (b, b-1)) 10
-- Resultado: [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

# find: 

find :: Foldable t => (a -> Bool) -> t a -> Maybe a - Essa função recebe uma função condicional (a -> Bool) e um tipo Foldable t a, e retorna o primeiro elemento que satisfaz a condição ou Nothing caso não haja nenhum elemento que satisfaça. Exemplo de uso:

```haskell
find :: Foldable t => (a -> Bool) -> t a -> Maybe a
find p = foldr (\x acc -> if p x then Just x else acc) Nothing
```

```
find even [1, 3, 5, 7, 8, 10]
-- Resultado: Just 8
```

# partition: 

partition :: (a -> Bool) -> [a] -> ([a], [a]) - Essa função recebe uma função condicional (a -> Bool) e uma lista [a], e retorna uma tupla com duas listas: a primeira contendo todos os elementos da lista original que satisfazem a condição e a segunda contendo os elementos restantes. Exemplo de uso:

```haskell
partition :: (a -> Bool) -> [a] -> ([a], [a])
partition p xs = (ts, fs)
  where
    (ts, fs) = foldr (\x (ts, fs) -> if p x then (x:ts, fs) else (ts, x:fs)) ([], []) xs
```

```
partition even [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
-- Resultado: ([2, 4, 6, 8, 10], [1, 3, 5, 7, 9])
```

# flip: 

flip :: (a -> b -> c) -> b -> a -> c - Essa função recebe uma função binária (a -> b -> c) e retorna uma nova função com os argumentos invertidos (b -> a -> c). Exemplo de uso:

```haskell
flip :: (a -> b -> c) -> b -> a -> c
flip f x y = f y x
```

```
let divide x y = x / y
flip divide 2 4
-- Resultado: 0.5
```

# compose: 

compose :: (b -> c) -> (a -> b) -> (a -> c) - Essa função recebe duas funções (b -> c) e (a -> b) e retorna uma nova função (a -> c) que é a composição das duas funções passadas como argumento. Exemplo de uso:

```haskell
(.)    :: (b -> c) -> (a -> b) -> a -> c
(.) f g = \x -> f (g x)
```

```
let f x = x + 1
let g x = x * 2
(compose g f) 3
-- Resultado: 8
```

# fix: 

fix :: (a -> a) -> a - Essa função recebe uma função (a ->a) e retorna uma função que retorna o resultado fixo da aplicação sucessiva da função ao seu próprio resultado. Exemplo de uso:

```haskell
fix :: (a -> a) -> a
fix f = let x = f x in x
```

```
let factorial x = if x == 0 then 1 else x * factorial (x-1)
fix factorial 5
-- Resultado: 120
```

# on: 

on :: (b -> b -> c) -> (a -> b) -> a -> a -> c  - Essa função é usada para aplicar uma função a dois argumentos a dois valores de forma infixada. Em outras palavras, permite a aplicação de uma função entre dois valores usando a notação infixada. Exemplo de uso:

```haskell
on :: (b -> b -> c) -> (a -> b) -> a -> a -> c
on f g x y = f (g x) (g y)
```

```
let max x y = if x > y then x else y
on max 2 4
-- Resultado: 4 
```
