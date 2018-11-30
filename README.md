luca 
d
MATCH (n:product_name)--(r:receipt)--(m:product_name)
WITH n,count(r) AS commun , m
MATCH (n:product_name)--(r:receipt)--()
WITH n,commun,count(r) AS degn,m
MATCH (m:product_name)--(r:receipt)--()
WITH n,commun,degn,count(r) AS degm ,m
RETURN n.Produit,m.Produit, 100* commun/(degm+degn-commun) AS sim ORDER BY sim DESC LIMIT 20

question e

MATCH (n:product_name)--(r:receipt)
WITH n,count(r) AS degn ORDER BY degn DESC LIMIT 1
MATCH (n:product_name)--(r:receipt)--(m:product_name)
WITH n,degn,COUNT(r) AS commun,m
MATCH (m:product_name)--(r:receipt)
WITH n,degn,commun,COUNT(r) AS degm,m
RETURN n.Produit,m.Produit,degn,degm,commun, 100*commun/(degm+degn-commun) AS sim ORDER BY sim DESC LIMIT 1


cree une boisson 
MATCH (m:Boisson),(n:product_name)
WHERE (n)--(:BoissonG) or  (n)--(:Vin) or (n)--(:Birra) or n.Produit=~'.*Vittel|.*Espresso.*|Caffè|.*Cappuccino.*|.*Latte.*|.*Thé.*|.*Menthe.*|Marocchino|.*Earl Grey.*|Macchiatto|.*Jasmin.*|Cioccolata - .|.*Simply.*|Camomille|.*Spritz.*|.*Chocolat Chaud.*|Liquide.*'
MERGE (m)-[:estuneBoisson]-(n)

question f

MATCH (m:product_name) WHERE not (m)-[:estuneBoisson]-()
WITH COLLECT (m) AS liste_plat
UNWIND liste_plat AS m
MATCH (n:product_name)--(r:receipt)--(m)
WHERE  (n)-[:estuneBoisson]-() 
RETURN m.Produit,n.Produit ,COUNT(r) AS commun ORDER BY commun DESC

question g
MATCH (m:product_name) WHERE not (m)-[:estuneBoisson]-()
WITH COLLECT (m) AS liste_plat
UNWIND liste_plat AS m
MATCH (n:product_name)--(r:receipt)--(m)
WHERE  (n)-[:estuneBoisson]-() 
WITH m,n ,COUNT(r) AS commun
MATCH (m:product_name)--(r:receipt)
WITH m,n,commun, count(r) AS degm
MATCH (n:product_name)--(r:receipt)
WITH  m,n,commun,degm, count(r) AS degn
RETURN m.Produit,n.Produit, 100*commun/(degm+degn-commun) AS sim ORDER BY sim DESC
