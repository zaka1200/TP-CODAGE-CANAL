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

Pour coder/décoder un message aléatoire avec un bruit AWGN

```matlab
function encoded_message = encode(message, generator_matrix)
    % message est le message à coder, generator_matrix est la matrice de génération utilisée pour coder le message
    encoded_message = mod(message*generator_matrix, 2);
end

function decoded_message = decode(received_message, parity_check_matrix)
    % received_message est le message reçu avec bruit, parity_check_matrix est la matrice de vérification utilisée pour décoder le message
    syndrome = mod(received_message*parity_check_matrix, 2);
    [~, error_index] = ismember(syndrome, error_matrix, 'rows');
    error_vector = zeros(1, n);
    error_vector(error_index) = 1;
    decoded_message = mod(received_message + error_vector, 2);
end
```

- La première fonction, encode, prend en entrée le message aléatoire et la matrice de génération pour coder le message et retourne le message codé. La deuxième fonction, decode, prend en entrée le message reçu avec bruit et la matrice de vérification pour décoder le message, puis calcule le syndrome en multipliant le message reçu avec bruit avec la matrice de vérification. Ensuite, elle utilise la fonction ismember pour trouver l'index de l'erreur dans la matrice d'erreur, puis crée le vecteur d'erreur à partir de cet index et ajoute ce vecteur d'erreur au message reçu avec bruit pour obtenir le message décodé.  	

- Dans la suite il faut coder/décoder un message forme par des mots generes aleatoirement, en rajoutant un bruit AWGN entre les etapes de codage et de decodage

```matlab
>> ebn0 = 0:7
>> BER = zeros(size(ebn0))
>> BER_theorique = zeros(size(ebn0))
```

> la simulation rapport eb/n0 allant de 0 à 7 dB

```matlab
for i = 1:length(ebn0)
    messgae=floor(rand(1,k).*2)
    code_message =rem(message*G,2)
    code_message_bruit = code_message+randn(size(code_message))*sqrt(1/(2*rho(i)))
    message_decode=rem(code_message_bruit*H,2)
    message = message(:,1:size(message_decode,2))
    BER(i)= sum(message~=message_decode)/L
    BER_theorique(i)=0.5*erfc(sqrt(10^(ebn0(i)/10)))
end
```
- La boucle génère des mots messages aléatoires de longueur 4, puis les code, ajoute du bruit gaussien, les décode, calcule la probabilité d'erreur et la compare à la probabilité d'erreur théorique. Enfin, il trace un graphique montrant la probabilité d'erreur avec et sans codage.

```matlab
>> figure
semilogy(ebn0,BER,'-o',ebn0,BER_theorique,'-*')
xlabel('Eb/No (dB)')
ylabel('BER')
legend('Avec codege','sans codage')
```

![image](https://user-images.githubusercontent.com/121964432/214446626-3332914d-cd5c-40ba-bd18-90bee13e8ab2.png)



- La performance de transmission est considérablement améliorée lorsque l'on utilise un codage, comme le montre la courbe de BER (Ber Error Rate). On peut observer que la courbe de BER théorique obtenue avec codage est similaire à celle obtenue par simulation, ce qui démontre que l'utilisation de l'ECC (Error Correction Code) avec la matrice génératrice systématique G et la matrice de contrôle de parité H permet de corriger efficacement les erreurs de transmission dans un canal bruité.

# Code de Hamming:

Générez un code de Hamming C(7,4) grâce à la fonction hammgen() :

```matlab
>> [H,G,n,k]=hammgen(3)

H =

     1     0     0     1     0     1     1
     0     1     0     1     1     1     0
     0     0     1     0     1     1     1


G =

     1     1     0     1     0     0     0
     0     1     1     0     1     0     0
     1     1     1     0     0     1     0
     1     0     1     0     0     0     1


n =

     7


k =

     4

```

- Codez les bits émis avec la fonction encode() :

```matlab
>> msg=randi([0 1],1,k)

msg =

     1     1     0     1
     
     
>> motcode=encode(msg,n,k)

motcode =

     0     0     0     1     1     0     1
```

Au départ, un message aléatoire composé de 0 et 1 de longueur k est généré. Ensuite, la fonction encode est utilisée pour coder ce mot émis.

- A la reception, decodage des bits emis a l aide  la fonction decode()

```matlab
>> motdecode=decode(motcode,n,k)

motdecode =

     1     1     0     1
```


- On génère un mot aléatoirement de la même façon qu'auparavant :

```matlab
data =
    1x4 logical array
    0   1   1   0
```

- Par suite on encode par la fonction encode :

```matlab
>> encoded=encode(data,n,k,'hamming/binnary')
encoded  =
        1   0   0   0   1   1   0
      
```
# Code cyclique

```matlab
>> message = [1   1   1   0   0   0   1   1]
 
 message =
            1   1   1   0   0   0   1   1
>> poly =[1 1   0   0   1   1]
  poly  =
        1   1   0   0   1   1
 >> code = deconv(message,poly)
  code =
        1   0   1
```

Les codes cycliques sont des codes de correction d'erreur qui utilisent les propriétés des polynômes pour détecter et corriger les erreurs. Ils peuvent détecter toutes les erreurs de poids inférieur ou égal à t et corriger jusqu'à t erreurs par bloc de n bits. Pour choisir le meilleur code pour une application spécifique, il est important de considérer à la fois le pouvoir correcteur et le taux d'utilisation de la capacité de codage. Par exemple, le code C(15,5) a le plus grand pouvoir correcteur (t=3) parmi les codes présentés, mais il a un taux d'utilisation moins élevé de la capacité de codage. Alors que le code C(15,7) a un pouvoir correcteur moins élevé mais un taux d'utilisation plus élevé de la capacité de codage.
