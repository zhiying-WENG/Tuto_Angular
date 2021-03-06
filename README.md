# Découverte du framework Angular
- [Partie 1 : Initiation](#partie-1--initiation)
  - [1.1 Create the product list](#11-create-the-product-list)
  - [1.2 Pass data to a child component](#12-pass-data-to-a-child-component)
  - [1.3 Pass data to a parent component](#13-pass-data-to-a-parent-component)
    
- [Partie 2 : Routing](#partie-2--routing)
  - [2.1 Associate a URL path with a component](#21-associate-a-url-path-with-a-component)
  - [2.2 View product details](#22-view-product-details)
    
- [Partie 3 : Data](#partie-3--data)
  - [3.1 Define a cart service](#31-define-a-cart-service)
  - [3.2 Use the cart service](#32-use-the-cart-service)
  - [3.3 Create the cart view](#33-create-the-cart-view)
  - [3.4 Retrieve shipping prices](#34-retrieve-shipping-prices)
  
- [Partie 4 : Formulaire](#partie-4--formulaire)
  - [4.1 Define the checkout form model](#41-define-the-checkout-form-model)
  - [4.2 Create the checkout form](#42-create-the-checkout-form)


## Partie 1 : Initiation


### 1.1 Create the product list

#### 1.1.1

*ngFor  un peu comment  boucle for dans JS.  Ici ajouter `<div>...</div>` pour chaque product dans products into DOM. `{{ }}` permet de rendre la valeur de la propriété sous forme de texte.
```
<div *ngFor="let product of products">

  <h3>
      {{ product.name }}
  </h3>

</div>
```
un peu équivalant JS:
```
for (let product of products){
    content +=`
        <div>
            <h3>
                ${product.name}
            </h3>
        </div>`
}
```

#### 1.1.2
```
<a [title]="product.name + ' details'">
```
[] permettre binding propriété avec data,  ici propriété "title" de `<a>` binding avec name of product concat String details

un peu équivalent JS:
```
<a title="${product.name} details">
```

#### 1.1.3

*ngIf condition un peu comment  if dans JS. 
```
<p *ngIf="product.description">
    Description: {{ product.description }}
</p>
```
Ici si product.product.description n'exist pas (condition est false) `<p>...</p>`sera pas créer

un peu équivalant JS:
```
if (product.description){
    content +=`
        <p>
            Description: ${product.description}
        </p>`
}
```

#### 1.1.4
```
<button (click)="share()">
    Share
</button>
```
Ici event click binding avec method share(), si on click button "Share",  it will call method share()

### 1.2 Pass data to a child component

#### 1.2.1
```
ng generate component product-alerts
```
Ce ligne de commande permettre créer new component product-alerts.  Ce component compris 3 fiches init  ( .ts, .html et .css ).

#### 1.2.2
```
import { Input } from '@angular/core';
import { Product } from '../products';
```
Permettre ce component peut recevoir des donnes de product (from products.ts)

#### 1.2.3
```
@Input() product!: Product;
```
Decorator @Input() indiquer value de propriété product est recevoir passer par component parent

"!" est non-null assertion operator, donc ce ligne de code equivalant:

```
@Input() product: Product | undefined;
```

#### 1.2.4

_app.module.ts_
```
import { ProductAlertsComponent } from './product-alerts/product-alerts.component';
@NgModule({
  declarations: [
    AppComponent,
    TopBarComponent,
    ProductListComponent,
    ProductAlertsComponent,
  ],
```
Permettre ProductAlertsComponent peut être utiliser par autre components dans l'application

#### 1.2.5

_product-list.component.html_
```
<app-product-alerts
  [product]="product">
</app-product-alerts>
```
Permettre ProductAlertsComponent reconnu et affiche comment enfant de ProductListComponent. Parent component passer courant product comment input pour enfant component.

### 1.3 Pass data to a parent component

#### 1.3.1
```
export class ProductAlertsComponent {
  @Input() product: Product | undefined;
  @Output() notify = new EventEmitter();
}
```
Créer un instance de EventEmitter(). Decorator @Ouput() permettre ProductAlertsComponent emmit un event quand value de propriété instance changer.

#### 1.3.2

_product-alerts.component.html_
```
<p *ngIf="product && product.price > 700">
  <button (click)="notify.emit()">Notify Me</button>
</p>
```
Pour product avoir prix superieur à 700  ajouter un button "Notify Me",  ce button event click binding method notify.emit().

#### 1.3.3

_product-list.component.ts_
```
export class ProductListComponent {

  products = products;

  share() {
    window.alert('The product has been shared!');
  }

  onNotify() {
    window.alert('You will be notified when the product goes on sale');
  }
}
```
Definie method onNotify() dans product-list.component.ts ( component parent ) celui binding avec event click de ProductAlertsComponent ( component enfant )

#### 1.3.4

_product-list.component.html_
```
<app-product-alerts
  [product]="product" 
  (notify)="onNotify()">
</app-product-alerts>
```
Parent component ProductListComponent recevoir data par son enfant component ProductAlertsComponent. 

Click button "Notify Me" --> call method notify.emit() --> call method onNotify() dans parent component --> afficher une alert 'You will be notified when the product goes on sale'


## Partie 2 : Routing


### 2.1 Associate a URL path with a component

#### 2.1.1

_app.module.ts_
```
@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
    ])
  ],
```
Route pour product details:

path 'products/:productId' associer ProductDetailsComponent

Par ex: http://localhost:4200/products/1  

si productId est 1

#### 2.1.2

_product-list.component.html_
```
<a
    [title]="product.name + ' details'"
    [routerLink]="['/products', product.id]">
    {{ product.name }}
</a>
````
Routerlink (lien `<a>`) composer par root/products/product.id

product.id est parametre

Par ex: http://localhost:4200/products/1


### 2.2 View product details

_product-details.component.ts_
```
import { ActivatedRoute } from '@angular/router';
import { Product, products } from '../products';

export class ProductDetailsComponent implements OnInit {
  product: Product | undefined;

  constructor(private route: ActivatedRoute) { }

  ngOnInit(): void {
    // First get the product id from the current route.
    const routeParams = this.route.snapshot.paramMap;
    const productIdFromRoute = Number(routeParams.get('productId'));

    // Find the product that correspond with the id provided in route.
    this.product = products.find(product => product.id === productIdFromRoute);
  }

}
```
Inject ActivatedRoute into the constructor() par parametre "private route: ActivatedRoute"

Method ngOnInit() occuper les parametres de currant rout (Type ActivatedRout), prendre productId dans les parametre. Trouver product dans products celui product.id correspond productId dans les parametres de rout currant.


## Partie 3 : Data


### 3.1 Define a cart service

_cart.service.ts_
```
import { Product } from './products';
/* . . . */
export class CartService {
  items: Product[] = [];
/* . . . */
}
```
Propriété items stocker les currant products dans cart as array (init array est vide).

### 3.2 Use the cart service

#### 3.2.1

_product-details.component.ts_
```
import { CartService } from '../cart.service';

export class ProductDetailsComponent implements OnInit {

  constructor(
    private route: ActivatedRoute,
    private cartService: CartService
  ) { }
}
```
Pour use cart service : Import CartService par cart.service.ts 

Inject the cart service  into the constructor() comment parametre "private cartService: CartService"

#### 3.2.2

_product-details.component.html_
```
<button type="button" (click)="addToCart(product)">Buy</button>
```
Click button "Buy" --> call method addToCart(product) dans product detail --> call method addToCart(product) dans cart service --> product currant ajouter dans cart + afficher un alert 'Your product has been added to the cart!'

### 3.3 Create the cart view

_app.module.ts_
```
@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
      { path: 'cart', component: CartComponent },
    ])
  ],
```
Ajouter route pour CartComponent avec path: 

root/cart

Par ex: http://localhost:4200/cart

### 3.4 Retrieve shipping prices

_cart.service.ts_
```
import { HttpClient } from '@angular/common/http';

export class CartService {
  items: Product[] = [];

  constructor(
    private http: HttpClient
  ) {}
/* . . . */
}
```
Import HTTPClient par package '@angular/common/http'.

Inject HttpClient into the CartService constructor() comment un parametre "private http: HttpClient"


## Partie 4 : Formulaire

### 4.1 Define the checkout form model

_cart.component.ts_
```
import { FormBuilder } from '@angular/forms';

export class CartComponent {

  items = this.cartService.getItems();

    constructor(
    private cartService: CartService,
    private formBuilder: FormBuilder,
    ) {}

    checkoutForm = this.formBuilder.group({
        name: '',
        address: ''
    });

    onSubmit(): void {
        // Process checkout data here
        this.items = this.cartService.clearCart();
        console.warn('Your order has been submitted', this.checkoutForm.value);
        this.checkoutForm.reset();
    }
}
```
Import the FormBuilder service par  @angular/forms package. Cet service provider convenient methods pour generating controls.  

Inject the FormBuilder service dans constructor()  

Utiliser method group() de FormBuilder pour former un form model contain "name" et "address" 

### 4.2 Create the checkout form

_cart.component.html_
```
<form [formGroup]="checkoutForm" (ngSubmit)="onSubmit()">

  <div>
    <label for="name">
      Name
    </label>
    <input id="name" type="text" formControlName="name">
  </div>

  <div>
    <label for="address">
      Address
    </label>
    <input id="address" type="text" formControlName="address">
  </div>

  <button class="button" type="submit">Purchase</button>

</form>
```
Propriété formGroup de `<form>` binging to checkoutForm 

FormControlName binging to checkoutForm form controls

Event ngSubmit binging to method onSubmit()

