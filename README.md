luca 
nathan
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

question h
MATCH (m:product_name) WHERE not (m)-[:estuneBoisson]-()
WITH COLLECT (m) AS liste_plat
UNWIND liste_plat AS m
MATCH (n:product_name)--(r:receipt)--(m)
WHERE  (n)-[:estunVin]-() 
WITH m,n ,COUNT(r) AS commun
MATCH (m:product_name)--(r:receipt)
WITH m,n,commun, count(r) AS degm
MATCH (n:product_name)--(r:receipt)
WITH  m,n,commun,degm, count(r) AS degn
RETURN m.Produit,n.Produit, 100*commun/(degm+degn-commun) AS sim ORDER BY sim DESC LIMIT 10

USING PERIODIC COMMIT 10
LOAD CSV WITH HEADERS FROM "file:///export-IT-1sur4-sans-boisson.csv" AS row
MERGE (r:receipt{Client:row.receipt})
SET r.Client_nouveau = "Client_nouveau"

USING PERIODIC COMMIT 10
LOAD CSV WITH HEADERS FROM "file:///export-IT-1sur4-sans-boisson.csv" AS row
MATCH (r:receipt{Client:row.receipt})
MATCH (p:product_name{Produit:row.product_name})
MERGE (r)-[c:commande]->(p)
SET c.quantite = row.quantity
SET c.date = row.date
SET p.prix = row.price

i
MATCH (n:receipt)
WHERE exists(n.Client_nouveau) 
WITH COLLECT (n) AS liste_clientnv
UNWIND liste_clientnv AS n
MATCH (n:receipt)--(p:product_name)--(m:receipt)
WHERE not(exists(m.Client_nouveau))
WITH n,m,COUNT(p) AS commun
MATCH (m:receipt)--(p:product_name)
WITH n,m,commun, count(p) AS degm
MATCH (n:receipt)--(p:product_name)
WITH  n,m,commun,degm, count(p) AS degn
RETURN n,m, 100*commun/(degm+degn-commun) AS sim ORDER BY sim DESC LIMIT 20

j
MATCH (n:receipt)
WHERE exists(n.Client_nouveau) 
MATCH (n:receipt)--(p:product_name)--(m:receipt)
WHERE not(exists(m.Client_nouveau))
WITH n,m,COUNT(p) AS commun
MATCH (m:receipt)--(p:product_name)
WITH n,m,commun, count(p) AS degm
MATCH (n:receipt)--(p:product_name)
WITH  n,m,commun,degm, count(p) AS 
degn
with n,m, 100*commun/(degm+degn-commun) AS sim ORDER BY sim DESC
WITH n,COLLECT([m,sim]) AS liste_clientnv
return n, liste_clientnv[..10]




k
MATCH (n:receipt)
WHERE exists(n.Client_nouveau) 
MATCH (n:receipt)--(p:product_name)--(m:receipt)
WHERE not(exists(m.Client_nouveau))
WITH n,m,COUNT(p) AS commun
MATCH (m:receipt)--(p:product_name)
WITH n,m,commun, count(p) AS degm
MATCH (n:receipt)--(p:product_name)
WITH  n,m,commun,degm, count(p) AS degn
with n,m, 100*commun/(degm+degn-commun) AS sim ORDER BY sim DESC
WITH n,COLLECT(m) AS liste_client
UNWIND liste_client[..10]  AS m 
MATCH (m:receipt)-[c:commande]-(p:product_name)
WHERE not (p)-[:estuneBoisson]-()
WITH n,p,sum(toInteger(c.quantite)) as qte ORDER by qte DESC
RETURN n,collect([p.Produit,qte]) AS liste_plat

l

MATCH (n:receipt)
WHERE exists(n.Client_nouveau) 
MATCH (n:receipt)--(p:product_name)--(m:receipt)
WHERE not(exists(m.Client_nouveau))
WITH n,m,COUNT(p) AS commun
MATCH (m:receipt)--(p:product_name)
WITH n,m,commun, count(p) AS degm
MATCH (n:receipt)--(p:product_name)
WITH  n,m,commun,degm, count(p) AS degn
with n,m, 100*commun/(degm+degn-commun) AS sim ORDER BY sim DESC
WITH n,COLLECT(m) AS liste_client
UNWIND liste_client[..10]  AS m 
MATCH (m:receipt)-[c:commande]-(p:product_name)
WHERE not (p)-[:estuneBoisson]-() and not(n)--(p)
WITH n,p,sum(toInteger(c.quantite)) as qte ORDER by qte DESC
WITH n,collect([p.Produit,qte]) AS liste_plat
RETURN n,liste_plat[..1]
