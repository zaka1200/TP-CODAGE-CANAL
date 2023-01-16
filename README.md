# TP-CODAGE-CANAL

## Objectif : Etude de la transmission binaire dans un canal idéal et  évaluation des performances en fonction du rapport Eb/N0 avec et  sans codage canal

L’objectif du TP est d’observer les améliorations apportées par les codes correcteurs d’erreur (ECCs pour Error Correcting Codes) sur les performances des transmissions numériques. Les performances sont exprimées en termes de BER (Bit Error Rate) et les simulations sont effectuées dans le cadre d’une transmission dans un canal AWGN (Additive White Gaussian Noise) de densité spectrale de puissance N0/2..
Trois types de codes sont étudiés : les codes en blocs linéaires à base de matrice génératrice, les codes cycliques, et les codes convolutifs. Le principe des simulations consiste à comparer les performances de deux chaînes de transmission : avec et sans ECCs. Ces performances sont évaluées en termes de BER en fonction du rapport Eb/N0 où Eb désigne l’énergie moyenne reçue par bit et où N0/2 désigne la densité spectrale de puissance du bruit additif blanc gaussien.

# Code en bloc linéaire C(7,4)

Pour créer la matrice génératrice systématique G à partir de la matrice de parité P en utilisant Matlab, vous pouvez utiliser la commande "eye" pour créer la matrice identité de taille k (où k est la longueur du code) et la combiner avec la matrice P en utilisant la commande "horzcat"

```matlab
P=[1 1 0
    0 1 1 
    1 0 1
    1 1 1];

[K,B]=size(P); % longueur du code
I = eye(k); % matrice identité de taille k
G = horzcat(I, P); % matrice génératrice systématique
```
Pour créer la matrice de contrôle de parité H à partir de la matrice génératrice systématique G , on va utiliser la commande "null"

```matlab
H=abs(transpose(null(G,'rational')))
```

Cette commande trouvera une base pour l'espace null de G, qui est équivalente à la matrice de contrôle de parité H. Il est important de noter que cette commande utilise le rang de la matrice pour trouver la base de la nullité, il est donc important que la matrice G ne soit pas singulière pour que cette commande fonctionne correctement.

- resultas :

```matlab
G =

     1     0     0     0     1     1     0
     0     1     0     0     0     1     1
     0     0     1     0     1     0     1
     0     0     0     1     1     1     1
     

H =

     1     0     1     1     1     0     0
     1     1     0     1     0     1     0
     0     1     1     1     0     0     1
```

pour s'assurer que la matrice H est bien une matrice de contrôle de parité, on peut vérifier que G*H' = 0

```matlab
GH = G*H';

```

 GH est bien égal à 0 cela signifie que G et H sont bien des matrices génératrice et de contrôle de parité
 
 ```matlab
K=4; % Longueur des mots information message L=2^K; % nombre de mots codes
for i=1:L
    M(i,:)=de2bi(i-1,K,'left-msb'); 
end
code=rem(M*G,2) % La table des mots codes
```
 
- Le code Matlab suivant définit une variable K égale à 4, qui représente la longueur des mots d'information (également appelés mots de message). La variable L est ensuite définie comme étant 2 élevé à la puissance de K, ce qui est utilisé pour calculer le nombre de mots de code.

- La ligne suivante utilise une boucle for pour itérer à travers toutes les valeurs de i de 1 à L. Pour chaque itération, la fonction de2bi est appelée avec la valeur actuelle de i moins 1 comme entrée et K comme nombre de bits. L'option 'left-msb' est utilisée pour spécifier que le bit le plus significatif est du côté gauche. La sortie de la fonction est stockée dans la variable M à la ligne et à la colonne correspondantes.

- Enfin, la variable code est définie comme le résultat de la multiplication matricielle de M et G, prise modulo 2. On obtient ainsi le tableau des mots code.
- resultas :

```matlab
M = 
    0   0   0   0
    0   0   0   1
    0   0   1   0
    0   0   1   1
    0   1   0   0
    0   1   0   1
    0   1   1   0
    0   1   1   1
    1   0   0   0
    1   0   0   1
    1   0   1   0
    1   0   1   1
    1   1   0   0
    1   1   0   1
    1   1   1   0
    1   1   1   1
    
code =
    0   0   0   0   0   0   0
    0   0   0   1   1   1   1
    0   0   1   0   1   0   1
    0   0   1   1   0   1   0  
    0   1   0   0   0   1   1
    0   1   0   1   1   0   0
    0   1   1   0   1   1   0
    0   1   1   1   0   0   1
    1   0   0   0   1   1   0
    1   0   0   1   0   0   1
    1   0   1   0   0   1   1
    1   0   1   1   1   0   0
    1   1   0   0   1   0   1
    1   1   0   1   0   1   0
    1   1   1   0   0   0   0
    1   1   1   1   1   1   1
    
```

Pour déterminer toutes les erreurs possibles de poids 1 (et inférieur) et en déduire la table des syndromes, on va utiliser le code Matlab suivante :

```matlab
    [n, k] = size(H);
    error_matrix = zeros(n, n);
    for i = 1:n
        error_vector = zeros(1, n);
        error_vector(i) = 1;
        error_matrix(i,:) = mod(error_vector*H, 2);
    end
    syndrome_table = error_matrix(:,k+1:n);
```

ce code prend en entrée la matrice de controle utilisée pour générer le code, on utilise une boucle pour générer toutes les erreurs possibles de poids 1 (et inférieur) en multipliant chaque vecteur d'erreur de poids 1 avec la matrice de controle, et on enregistre le résultat dans la matrice error_matrix. Ensuite, on extrait les colonnes k+1 à n de error_matrix pour obtenir la table des syndromes.

- resulats :

```matlab
error_matrix = 

    0   0   0   0   0   0   0
    1   0   0   0   0   0   0
    0   1   0   0   0   0   0
    0   0   1   0   0   0   0
    0   0   0   1   0   0   0
    0   0   0   0   1   0   0
    0   0   0   0   0   1   0
    0   0   0   0   0   0   1
    
syndrome_table =

    0   0   0
    1   1   0
    0   1   1
    1   0   1
    1   1   1
    1   0   0
    0   1   0
    0   0   1
    
```


