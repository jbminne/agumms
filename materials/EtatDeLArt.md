# Simulation num�rique de l'usinage : un �tat de l'art en vue de la constitution d'une base de donn�es mat�rielles

## I Contexte
Une op�ration d'usinage par machine-outil � commande num�rique sollicite des moyens m�caniques souvent complexes, on�reux et n�cessite une vigilance accrue pour minimiser les co�ts de fabrication. Il est alors primordial de pouvoir simuler les diff�rentes op�rations d'usinage avant de lancer la fabrication pour  
- v�rifier le mouvement des pi�ces usin�es et des outils afin de d�tecter d'�ventuelles collisions pendant le processus ;
- estimer le volume de mati�re enlev�e ;
- certifier la trajectoire d'outil g�n�r�e par un logiciel de FAO, en conformit� avec la machine.

Ces simulations sont g�n�ralement effectu�es par un logiciel de FAO � partir de mod�les g�om�triques [1]. Cependant, elles souffrent d'un manque de pr�cision concernant la g�om�trie obtenue et ne permettent pas d'anticiper certains probl�mes (aptitude � l'usinage, modes d'usure et d'endommagement des outils, qualit� de surface de la pi�ce usin�e).

Afin d'optimiser les conditions d'usinage, diff�rentes m�thodes peuvent �tre mises en oeuvre :  
- les m�thodes exp�rimentales, longues et co�teuses, o� la zone de fonctionnement id�ale est recherch�e au travers d'un plan d'exp�rience ;
- les m�thodes num�riques, utilisant un mod�le de coupe � une �chelle d'observation particuli�re (cf. figure 1).

![echelles](https://github.com/pfelecan/agumms/blob/master/materials/echelles.png)

||
|:-:|
|Figure 1 : Les diff�rentes �chelles de mod�lisation de la coupe des m�taux [4]. A l'�chelle microscopique, la cartographie EBSD d�voile la microstructure du copeau dans la zone de contact avec l'outil. |
|| 

Ces mod�les sont bas�s sur diff�rentes approches :  
- les approches empiriques, bas�es sur un grand nombre de donn�es exp�rimentales, comme le couple outil-mati�re (�chelle macroscopique) ;
- les approches ph�nom�nologiques ou m�canistes, bas�es sur des mod�lisations simples retranscrivant le comportement m�canique (�chelle m�soscopique) ;
- les approches physiques (�chelle microscopique) ou thermom�caniques (�chelle m�soscopique), fond�es sur des lois de comportement plus ou moins �volu�es.

Ces mod�les permettent de pr�dire la g�om�trie des copeaux, les forces de coupe et les �chauffements � partir des conditions de coupe et des mat�riaux usin�s et d'outil. En particulier, l'�nergie sp�cifique de coupe (aussi appel�e pression sp�cifique de coupe) $K_c$ est un param�tre important car il permet d'avoir acc�s � l'effort de coupe $F_c$, � la puissance de coupe $P_c$ et au couple � la broche.

Les approches empiriques relient $K_c$ (W.s.m<sup>-3</sup>) � l'�paisseur du copeau $\ell$ (mm) par une fonction analytique :

$$ 
K_c = K_{c1}\times \ell^{-m_c} 
$$

Les param�tres $K_{c1}$ et $m_c$ sont d�termin�s exp�rimentalement et tabul�s.

Les approches m�canistes permettent d'avoir acc�s aux efforts locaux � partir des conditions de coupe locales. Dans le cas de la coupe orthogonale (cf. figure 2), le mod�le de Merchant consid�re que la formation du copeau s'effectue par cisaillement. Il permet de calculer l'effort de coupe $F_c$ � partir des param�tres g�om�triques de la coupe (avance $s$, profondeur de passe $w$, angle de coupe $\gamma$), et de param�tres mat�riels (l'angle d'adh�rence $\Phi$ dans le cas d'un frottement de Coulomb et la contrainte maximale de cisaillement admissible $k$ pour un mat�riau rigide parfaitement plastique) :

$$
F_c = 2ksw\tan\left( \frac{\pi}{4}+\frac{\Phi - \gamma}{2}\right)
$$

Le param�tre $k$ est proportionnel � la limite �lastique et � la duret�. Le mod�le donne aussi une estimation de l'�paisseur du copeau et de la longueur de contact entre le copeau et l'outil (toutes deux proportionnelles � l'avance $s$). A partir de l'effort de coupe, on peut alors calculer l'�nergie sp�cifique de coupe $K_c = \frac{F_c}{sw}$ (W.s.m<sup>-3</sup>), la puissance de coupe $P_c = K_cQ$ (W) avec $Q=swV_c$ (m<sup>3</sup>.s<sup>-1</sup>) le d�bit de mati�re et $V_c$ la vitesse de coupe et enfin le couple � la broche $C=\frac{P_c}{\omega}$ (N.m) pour une vitesse angulaire $\omega$ (cas du tournage).

Ce mod�le a tendance � sous-estimer les efforts de coupe, l'�paisseur du copeau et la longueur de contact. De plus, les mod�les purement m�caniques ne pr�disent pas la d�croissance de $K_c$ avec l'avance $s$.

![geometrie](https://github.com/pfelecan/agumms/blob/master/materials/geometrie.png)

||
|:-:|
|Figure 2 : G�om�trie de la coupe orthogonale [2].|
|| 

Les approches thermom�caniques permettent d'avoir acc�s aux d�formations locales et aux contraintes r�siduelles de la pi�ce et de l'outil. Elles permettent de pr�dire pr�cis�ment la g�om�trie finale de la pi�ce et d'avoir acc�s � certaines grandeurs qualifiant l'�tat m�canique des composants (duret�, usure...). Elles sont g�n�ralement mises en oeuvre par simulation num�rique � l'aide de la m�thode des �l�ments finis. 

## II Simulation num�rique de l'usinage
### II.1 Objectif et difficult�s
L'objectif de la simulation num�rique de l'usinage est d'apporter une contribution � la compr�hension des ph�nom�nes li�s � la coupe des m�taux, en particulier dans la zone de contact entre l'outil et le mat�riau � usiner. Elle permet de fournir la g�om�trie du copeau et une estimation de la distribution des grandeurs physiques dans le mat�riau usin� : vitesse de d�placement, tenseur des vitesses de d�formation et des contraintes, d�formation plastique, temp�rature, voire d'autres grandeurs caract�risant l'�volution m�tallurgique du mat�riau usin� (nature des phases, taille des grains, duret�, contraintes r�siduelles...) ou son endommagement dans le copeau et en dessous de la surface cr��e par usinage. On peut aussi se proposer d'�tendre l'approche � l'outil (en le supposant en premi�re approximation thermo�lastique) pour y estimer le champ de contrainte et de temp�rature et ainsi aborder directement le probl�me de sa d�gradation et de son usure. Elles sont donc potentiellement tr�s riches et puissantes.

Plus pr�cis�ment, cela revient � estimer la valeur num�rique de ces grandeurs en des points particuliers associ�s � un maillage en construisant une solution approch�e du syst�me d'�quations d�crivant l��coulement de mati�re et de chaleur. Ce syst�me �tant non lin�aire, ces approches ne sont d�velopp�es que tr�s lentement et progressivement. L'usinage est l'une des configurations qui combinent en effet un grand nombre de difficult�s proprement num�riques :  
- g�om�trie du copeau ind�termin�e ;
- gestion du contact (les efforts de contacts et les surfaces en contact sont des inconnues du probl�me) ;
- gradients de d�formations, vitesses de d�formations et temp�ratures
tr�s �lev�s dans une zone confin�e ;
- couplage thermom�canique fort.

Sur le plan physique s'ajoutent, pour la confrontation des r�sultats � l'exp�rience, les incertitudes sur la rh�ologie du mat�riau usin� et la loi de frottement du copeau sur l�outil. Depuis les ann�es 1990, le d�veloppement des algorithmes de calcul par �l�ments finis et l'augmentation de la puissance de calcul des ordinateurs a permis de simuler directement le processus de formation du copeau en d�formation plane. De ce fait, la mod�lisation de l'usinage reste pour l'instant un domaine de recherche pointue et demande d'acqu�rir une expertise pouss�e dans diff�rents domaines tels que la coupe des m�taux, la m�canique des milieux continus, notamment en grandes transformations, la tribologie, la r�sistance des mat�riaux, la thermique, la m�tallurgie ainsi que la m�thode des �l�ments finis.

### II.2 Mod�lisation de la coupe orthogonale
La simulation de la coupe orthogonale reste � ce jour le seul cas d'usinage trait� num�riquement car les hypoth�ses de mod�lisation (�quilibre m�canique quasi-statique et r�gime thermique permanent de la phase stabilis�e d'usinage, d�formations planes en 2D) permettent de trouver un compromis acceptable entre le temps de calcul et la qualit� des r�sultats.

Le principe de la simulation d�une coupe orthogonale pure est donn� sur la figure 2 : la pi�ce est un bloc rectangulaire qui est bloqu� par une but�e ; l�outil est initialement � l�ext�rieur de la pi�ce et son ar�te est situ�e � une certaine distance de la surface sup�rieure de la pi�ce (avance $s$) . Anim� d�une vitesse horizontale (vitesse de coupe), l�outil rentre progressivement dans la pi�ce et le code de calcul fournit directement le processus de formation du copeau en d�but d�usinage en r�solvant simultan�ment les �quations m�caniques et l��quation de la chaleur.


Les principaux mod�les physiques utilis�s sont :  
- un mod�le de transfert de chaleur coupl� � la m�canique ;
- un mod�le rh�ologique thermo-�lasto-visco-plastique pour le mat�riau usin� et g�n�ralement thermo-�lastique pour l'outil ;
- un mod�le de frottement entre le mat�riau et l'outil (type Coulomb ou Tresca).

Le tableau 1 indique les principales propri�t�s indispensables pour une formulation thermo-�lasto-plastique (sans �crouissage).

 Propri�t�           | Symbole | Unit�  
:-------------------:|:-------:|:------:
 Masse volumique     | $\rho$  | kg.m<sup>-3</sup> 
 Module de Young     |   $E$    |   Pa  
 Coefficient de Poisson |$\nu$ |   -    
 Limite �lastique    | $R_e$ | Pa
 Coefficient de frottement|$\mu$| -
 Dilatation thermique|$\alpha$ | K<sup>-1</sup> 
 Chaleur sp�cifique  | $C_p$   | J.kg<sup>-1</sup>.K<sup>-1</sup>
 Conductivit� thermique|$\lambda$| W.K<sup>-1</sup>.m<sup>-1</sup>
 
||
|:-:|
|Tableau 1 : Propri�t�s thermiques et m�caniques.|
|| 

Etant donn� les forts gradients thermiques dans la zone de coupe (temp�ratures pouvant atteindre plusieurs centaines de degr�s Celsius), il est indispensable d'avoir acc�s aux variations des coefficients de ces mod�les avec la temp�rature. 

Il existe quantit�s de lois de comportement (visco-)plastiques. La loi de Johnson-Cook est souvent utilis�e pour mod�liser la visco-plasticit� de certains m�taux soumis � de grandes vitesses de d�formation (aciers bas et moyen carbone, aluminium, titane, laiton, cuivre, tungst�ne). Il relie la d�formation plastique $\varepsilon_p$ � la contrainte d'�coulement $\sigma_y$ ($T$ d�signe la temp�rature) :

$$
\sigma_y = \left[A+B\left(\varepsilon_p\right)^n\right]\left[1+C  \ln\left(\frac {\dot{\varepsilon}_p}{\dot{\varepsilon}_0}\right)\right]\left[1-\left(\frac{T-T_0}{T_f - T_0} \right)^m \right]
$$

Cette loi ph�nom�nologique compte 8 param�tres empiriques � identifier ($A$, $B$, $C$, $n$, $m$, $\dot{\varepsilon}_0$, $T_0$, $T_f$). Des variantes de cette loi existent, faisant intervenir encore plus de param�tres [3].  

D'autres mod�les peuvent �tre introduits :  
- un mod�le d'endommagement de la mati�re usin�e ;
- un mod�le de fissuration ;
- un mod�le m�tallurgique ;
- un mod�le d'�volution de la microstructure ;
- ...

Ces mod�les, parfois complexes, demandent une identification de leurs param�tres qui peut �tre fort d�licate.

Au sein de la m�thode des �l�ments finis, les principales m�thodes de r�solution utilis�es sont :  
- une m�thode de r�solution de l'�quilibre m�canique globale de la structure (type Newton) ;
- une m�thode de r�solution de l'�quilibre thermique (type $\theta$-m�thode) ;
- des m�thodes d'int�gration des lois de comportement des mat�riaux (implicites ou explicites) ;
- une m�thode de remaillage automatique (type ALE) ;
- une m�thode de gestion du contact (ma�tre-esclave, multiplicateurs de Lagrange,...).

Ces m�thodes peuvent s'av�rer tr�s chronophages voire r�dhibitoires en terme de m�moire et de temps de calcul (plusieurs dizaines d'heures pour la formation d'un copeau de 1 mm de long).

#### II.3 R�sultats atteignables

La simulation par �l�ments finis permet d'acc�der aux distributions de d�placement, d'effort de r�action, de vitesses de d�formation, de contraintes (cf. figure 3), de temp�ratures. Elle donne aussi acc�s � des param�tres directement utilisables par l'usineur :  
- la g�om�trie du copeau qui permet d'estimer le d�bit de mati�re usin�e ;
- l'effort de coupe qui permet d'estimer le couple � la broche et la puissance de coupe.

De plus, elle permet d'�tudier l'effet des divers param�tres de coupe (angle de coupe, vitesse de coupe, coefficient de frottement, avance, arrondi de l'ar�te de coupe) et permet de prendre en compte la g�om�trie exacte de l'outil (et d'un d�faut initial potentiel).

![mises](https://github.com/pfelecan/agumms/blob/master/materials/mises.png)

||
|:-:|
|Figure 3 : Distribution de la contrainte �quivalente de von Mises [3].|
|| 

### II.4 Limites actuelles et orientations futurs
La mod�lisation de l'usinage reste un domaine tr�s d�licat � mettre en donn�es car elle met en jeu des d�formations par cisaillement tr�s importantes, des temp�ratures de plusieurs centaines de degr�s et des vitesses de d�formation de l'ordre de plusieurs dizaines, voire centaines de milliers de s<sup>-1</sup>, ce qui rend tr�s difficile la mesure du comportement m�canique n�cessaire � la simulation de la coupe, car les dispositifs actuels qui permettent d'approcher de telles vitesses de d�formation (barres de Hopkinson) posent des probl�mes d'interpr�tation (h�t�rog�n�it� de d�formation, r�le du frottement en compression, �chauffement...). 

La simulation de la coupe orthogonale, seule configuration �tudi�e dans les travaux de recherche � l'heure actuelle, est g�n�ralement limit�e � quelques millim�tres d'usinage pendant quelques millisecondes. Elle n'a donc pas la pr�tention de pr�dire le comportement, l'usure, la dur�e de vie... de l�outil lors d'une op�ration d'usinage, mais contribue � la compr�hension des ph�nom�nes physiques impliqu�s.

Les recherches actuelles s'attachent � d�velopper des mod�les rh�ologiques et de frottement plus repr�sentatifs, notamment pour l'usinage � grande vitesse. Elles s'orientent vers l'int�gration de lois d'�volution de microstructure [3], d'usure, de prise en compte d'interaction fluide-structure (assistance � jet d'eau).

## III Constitution d'une base de donn�es mat�rielles pour l'usinage : sp�cifications et recommandations

Les mat�riaux sont g�n�ralement regroup�s en 4 grandes classes : les m�taux, les c�ramiques (regroup�es avec les verres), les polym�res (avec les �lastom�res) et les mat�riaux hybrides (composites, mousses, bois). Au sein de ces classes, on d�finit des familles (par exemple la famille des aciers pour la classe des m�taux). Ces familles sont elles-m�mes hi�rarchis�es en sous-structures suivant le niveau d'exhaustivit� qu'on recherche.

Comme les m�taux sont la classe la plus repr�sent�e des mat�riaux usin�s (en particulier, les aciers), on pourra les diviser en 6 grandes familles conform�ment � la norme ISO [5] :  
- les aciers ;
- les aciers inoxydables ;
- les fontes ;
- les alliages d'aluminium ;
- les alliages r�fractaires ;
- les aciers tremp�s.

Les mat�riaux de coupe sont principalement assimil�s � la classe des c�ramiques. On pourra diff�rencier les familles suivantes :  
- les aciers au carbone, au tungst�ne ;
- les carbures (c�ment�s rev�tus ou non, cermet) ;
- les c�ramiques ;
- les diamants.

Etant donn� le nombre pl�thorique de mat�riaux, en particulier de nuances au sein d'une m�me famille d'aciers, il n'est pas n�cessaire, dans un premier temps, de multiplier les sous-familles. De plus, pour chaque famille de mat�riau, on pourra donner soit une valeur repr�sentative, soit un intervalle de valeurs, des propri�t�s du tableau 1, � temp�rature ambiante. Comme les lois de comportement des mat�riaux usin�s et d'outil ne sont a priori pas connus (c'est � l'utilisateur de les d�finir), notamment concernant les propri�t�s visco-plastiques du mat�riau usin�, il n'est pas utile d'en proposer plus. 

On trouvera des exemples de base de donn�es mat�rielles sur Wikip�dia [6], comme Matweb [7] ou Matmatch [8]. La page Wikip�dia "Liste de propri�t�s d'un mat�riau" renvoie vers des pages contenant g�n�ralement des tableaux de valeur par type de mat�riaux. Ces valeurs pourront servir pour une mise en donn�es par d�faut d'une simulation simplifi�e, voire pour un mod�le m�caniste simplifi�, comme le mod�le de Merchant. Dans ce cas, la donn�e de $\mu = \tan \Phi$ et de $R_e=\sqrt{3}k$ est suffisante.

## R�f�rences
### Bibliographie
[1] Fikret Kalay, _Simulation num�rique de l'usinage - Application � l'aluminium AU4G (A2024-T351)_, Techniques de l'ing�nieur, BM 7002 (2010).
[2] Eric Felder, _Mod�lisation de la coupe des m�taux_, Techniques de l'ing�nieur, BM 7041 (2006).
[3] Shreyes Melkote, Wit Grzesik, Jos� Outeiro, Joel Rech, Volker Schulze, et al., _Advances in material and friction data for modelling of metal machining_, CIRP Annals - Manufacturing Technology, Elsevier, 2017, 66 (2), pp.731-754.
[4] C�dric Courbon. Vers une mod�lisation physique de la coupe des aciers sp�ciaux : int�gration du comportement m�tallurgique et des ph�nom�es tribologiques et thermiques aux interfaces. Th�se de doctorat. Ecole Centrale de Lyon (2011).

### Sitographie
[5] www.sandvik.coromant.com
[6] en.wikipedia.org/wiki/Materials_database
[7] www.matweb.com
[8] matmatch.com

