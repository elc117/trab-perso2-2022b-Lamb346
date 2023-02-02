# Funções de alta ordem em haskell

Funções de Alta Ordem são funções que possuem, como argumentos, outras funções ou retornam funções. Isso permite que elas possam ser aplicadas e combinadas de diversas formas, aumentando a flexibilidade e reusabilidade do código. Em Haskell, as funções de alta ordem são amplamente utilizadas, já que a linguagem é fortemente influenciada pela programação funcional. 

  

A seguir, serão apresentadas algumas funções de alta ordem comuns em Haskell não apresentadas em aula, explicando o que cada uma delas faz, suas definições(aproximadas) e como elas podem ser úteis na prática: 

## Funções

## span: 

span :: (a -> Bool) -> [a] -> ([a], [a]) - Essa função leva um predicado (a -> Bool) e uma lista [a], e retorna um par de listas, ([a], [a]), onde a primeira lista consiste em todos os elementos da lista original antes da primeira ocorrência de um elemento que não satisfazem a condição, e a segunda lista consiste nos elementos restantes.

Definição:
```haskell
span                    :: (a -> Bool) -> [a] -> ([a],[a])
span _ xs@[ ]            =  (xs, xs)
span p xs@(x:xs')
         | p x          =  let (ys,zs) = span p xs' in (x:ys,zs)
         | otherwise    =  ([ ],xs)
```
Exemplo de uso:
```
span (< 5) [1, 2, 3, 4, 5, 6, 7]
-- Resultado: ([1, 2, 3, 4], [5, 6, 7])
```

## iterate: 

iterate :: (a -> a) -> a -> [a] - Essa função leva uma função (a -> a) e um valor do tipo a, e retorna uma lista infinita de aplicações repetidas da função ao valor.

Definição:
```haskell
iterate :: (a -> a) -> a -> [a]
iterate f x =  x : iterate f (f x)
```
Exemplo de uso:
```
take 5 (iterate (*2) 1)
-- Resultado: [1, 2, 4, 8, 16]
```

## scanr:

scanr :: (a -> b -> b) -> b -> [a] -> [b] - Essa função leva uma função binária (a -> b -> b), um valor inicial do tipo b, e uma lista [a], e retorna uma lista de resultados intermediários obtidos pela dobragem da lista da direita com a função binária e o valor inicial.

Definição:
```haskell
scanr                   :: (a -> b -> b) -> b -> [a] -> [b]
scanr _ q0 []           =  [q0]
scanr f q0 (x:xs)       =  f x q : qs
                           where qs@(q:_) = scanr f q0 xs
```
Exemplo de uso:
```
scanr (+) 0 [1, 2, 3, 4]
-- Resultado: [10, 9, 7, 4, 0]
```

## scanl: 

scanl :: (b -> a -> b) -> b -> [a] -> [b] - Essa função leva uma função binária (b -> a -> b), um valor inicial do tipo b, e uma lista [a], e retorna uma lista de resultados intermediários obtidos pela dobragem da lista da esquerda com a função binária e o valor inicial.

Definição:
```haskell
-- scanl function
scanl :: (b -> a -> b) -> b -> [a] -> [b]
scanl _ q0 [] = [q0]
scanl f q0 (x:xs) = q : scanl f (f q x) xs
    where q = q0 `f` x
```
Exemplo de uso:
```
scanl (+) 0 [1, 2, 3, 4]
-- Resultado: [0, 1, 3, 6, 10]
```

## unfoldr: 

unfoldr :: (b -> Maybe (a, b)) -> b -> [a] - Essa função recebe uma função (b -> Maybe (a, b)) e um elemento inicial b, e retorna uma lista com os resultados da aplicação sucessiva da função ao elemento inicial. A função deve retornar Nothing quando não houver mais elementos para serem adicionados à lista.

Definição:
```haskell
unfoldr :: (b -> Maybe (a, b)) -> b -> [a]
unfoldr f b0 = build (\c n ->
  let go b = case f b of
               Just (a, new_b) -> a `c` go new_b
               Nothing         -> n
  in go b0)
```
Exemplo de uso:
```
unfoldr (\b -> if b == 0 then Nothing else Just (b, b-1)) 10
-- Resultado: [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

## find: 

find :: Foldable t => (a -> Bool) -> t a -> Maybe a - Essa função recebe uma função condicional (a -> Bool) e um tipo Foldable t a, e retorna o primeiro elemento que satisfaz a condição ou Nothing caso não haja nenhum elemento que satisfaça.

Definição:
```haskell
find :: Foldable t => (a -> Bool) -> t a -> Maybe a
find p = foldr (\x acc -> if p x then Just x else acc) Nothing
```
Exemplo de uso:
```
find even [1, 3, 5, 7, 8, 10]
-- Resultado: Just 8
```

## partition: 

partition :: (a -> Bool) -> [a] -> ([a], [a]) - Essa função recebe uma função condicional (a -> Bool) e uma lista [a], e retorna uma tupla com duas listas: a primeira contendo todos os elementos da lista original que satisfazem a condição e a segunda contendo os elementos restantes.

Definição:
```haskell
partition :: (a -> Bool) -> [a] -> ([a], [a])
partition p xs = (ts, fs)
  where
    (ts, fs) = foldr (\x (ts, fs) -> if p x then (x:ts, fs) else (ts, x:fs)) ([], []) xs
```
Exemplo de uso:
```
partition even [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
-- Resultado: ([2, 4, 6, 8, 10], [1, 3, 5, 7, 9])
```

## flip: 

flip :: (a -> b -> c) -> b -> a -> c - Essa função recebe uma função binária (a -> b -> c) e retorna uma nova função com os argumentos invertidos (b -> a -> c).

Definição:
```haskell
flip :: (a -> b -> c) -> b -> a -> c
flip f x y = f y x
```
Exemplo de uso:
```
let divide x y = x / y
flip divide 2 4
-- Resultado: 2
```

## compose: 

compose :: (b -> c) -> (a -> b) -> (a -> c) - Essa função recebe duas funções (b -> c) e (a -> b) e retorna uma nova função (a -> c) que é a composição das duas funções passadas como argumento.

Definição:
```haskell
(.)    :: (b -> c) -> (a -> b) -> a -> c
(.) f g = \x -> f (g x)
```
Exemplo de uso:
```
let f x = x + 1
let g x = x * 2
(compose g f) 3
-- Resultado: 8
```

## fix: 

fix :: (a -> a) -> a - Essa função recebe uma função (a ->a) e retorna uma função que retorna o resultado fixo da aplicação sucessiva da função ao seu próprio resultado.

Definição:
```haskell
fix :: (a -> a) -> a
fix f = let x = f x in x
```
Exemplo de uso:
```
let factorial x = if x == 0 then 1 else x * factorial (x-1)
fix factorial 5
-- Resultado: 120
```

## on: 

on :: (b -> b -> c) -> (a -> b) -> a -> a -> c  - Essa função é usada para aplicar uma função a dois argumentos a dois valores de forma infixada. Em outras palavras, permite a aplicação de uma função entre dois valores usando a notação infixada.

Definição:
```haskell
on :: (b -> b -> c) -> (a -> b) -> a -> a -> c
on f g x y = f (g x) (g y)
```
Exemplo de uso:
```
let max x y = if x > y then x else y
on max 2 4
-- Resultado: 4 
```
# Referências
[1] https://hackage.haskell.org/package/base-4.17.0.0/docs/Data-List.html#v:nub

[2] https://hackage.haskell.org/package/base-4.17.0.0/docs/Data-Function.html

[3] https://hackage.haskell.org/package/base-4.17.0.0/docs/src/GHC.Base.html

[4] https://hackage.haskell.org/package/base-4.17.0.0/docs/src/Data.Function.html

[5] https://chat.openai.com/chat (Dúvidas em relação ao funcionamento da definição das funções)
