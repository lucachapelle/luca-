luca 
d
MATCH (n:product_name)--(r:receipt)--(m:product_name)
WITH n,count(r) AS commun , m
MATCH (n:product_name)--(r:receipt)--()
WITH n,commun,count(r) AS degn,m
MATCH (m:product_name)--(r:receipt)--()
WITH n,commun,degn,count(r) AS degm ,m
RETURN n.Produit,m.Produit, 100* commun/(degm+degn-commun) AS sim ORDER BY sim DESC LIMIT 20
