# Tuto_Angular

## Partie 1 : Initiation

#### 1.1 Create the product list
1.1.1

*ngFor  un peu comment  boucle for dans JS,  ici ajouter `<div>` pour chaque produit dans products (Dans ses `<div>` affiche name of produit (ici `{{ product.name }}`) en form h3) into DOM. 
```
<div *ngFor="let product of products">

  <h3>
      {{ product.name }}
  </h3>

</div>
```
un peu equivalant JS:
```
for (let produit of produits){
    content +=`
        <div>
            <h3>
                ${produit.name}
            </h3>
        </div>`
}
```

1.1.2
```
<a [title]="product.name + ' details'">
```
[] permettre binding propréte avec data,  ici propréte "title" de `<a>` binding avec name of produit concat String details

un peu equivalent JS:
```
<a title="${product.name} details">
```

1.1.3

*ngIf condition un peu comment  if dans JS. 
```
<p *ngIf="product.description">
    Description: {{ product.description }}
</p>
```
ici si product.product.description n'exist pas (condition est false) `<p>...</p>`sera pas créer
un peu equivalant JS:
```
if (product.description){
    content +=`
        <p>
            Description: ${product.description}
        </p>`
}
```

1.1.4
```
<button (click)="share()">
    Share
</button>
```
ici event click binding avec method share(), si on click button "Share",  it will call method share()

#### 1.2 Pass data to a child component
1.2.1
```
ng generate component product-alerts
```
Ligne de commande "ng generate component product-alerts" permettre créer new component produit-alerts.  Ce component compris 3 fiches init  .ts,  .html  et   .css.

1.2.2
```
import { Component, OnInit, Input } from '@angular/core';
import { Product } from '../products';
```
Permettre ce component peut recevoir des donnes de product (from products.ts)

1.2.3
```
@Input() product!: Product;
```
Decorator @Input() indiquer value de propréte produit est recevoir passer par component parent

1.2.4

Dans fiche app.module.ts
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

1.2.5

Dans product-list.component.html
```
<app-product-alerts
  [product]="product">
</app-product-alerts>
```
Permettre ProductAlertsComponent reconnu et affiche comment enfant de ProductListComponent. Parent component passer courant product comment input pour enfant component.

#### 1.3 Pass data to a parent component
1.3.1
```
export class ProductAlertsComponent {
  @Input() product: Product | undefined;
  @Output() notify = new EventEmitter();
}
```
Créer un instance de EventEmitter(). Decorator @Ouput() permettre ProductAlertsComponent emmit un event quand value de propréte instance changer.

1.3.2

Dans product-alerts.component.html
```
<p *ngIf="product && product.price > 700">
  <button (click)="notify.emit()">Notify Me</button>
</p>
```
Pour product avoir prix superieur à 700  ajouter un button "Notify Me",  ce button action click binding method notify.emit().

1.3.3

Definie method onNotify() dans product-list.component.ts (component parent) celui binding avec event click avec ProductAlertsComponent(component enfant)
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
1.3.4

Dans product-list.component.html
```
<app-product-alerts
  [product]="product" 
  (notify)="onNotify()">
</app-product-alerts>
```
Parent component ProductListComponent recevoir data par son enfant component ProductAlertsComponent. 


## Partie 2 : Routing

## Partie 3 : Data

## Partie 4 : Formulaire