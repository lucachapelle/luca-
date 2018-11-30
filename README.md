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
