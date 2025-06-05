import { ComponentFixture, TestBed } from ‘@angular/core/testing’;
import { BrowserModule } from ‘@angular/platform-browser’;
import { LinkModule, SVGIconModule } from ‘@fmr-ap001330/fc-modern-angular-r’;
import { ActionType } from ‘../../../security/models/security.model’;
import { TradeRecommendationFacade } from ‘../../../store/trade-recommendation/r’;
import { TradeOrderSuccessComponent } from ‘./trade-order-success.component’;

describe(‘TradeOrderSuccessComponent’, () => {
let component: TradeOrderSuccessComponent;
let fixture: ComponentFixture<TradeOrderSuccessComponent>;
let mockOrderStepPresenter: jest.Mocked<any>;

beforeEach(async () => {
mockOrderStepPresenter = {
getCardInfoByAction: jest.fn()
};

```
await TestBed.configureTestingModule({
  imports: [BrowserModule, SVGIconModule, LinkModule],
  declarations: [TradeOrderSuccessComponent],
  providers: [
    { provide: 'OrderStepPresenter', useValue: mockOrderStepPresenter }
  ]
}).compileComponents();

fixture = TestBed.createComponent(TradeOrderSuccessComponent);
component = fixture.componentInstance;
```

});

it(‘should create’, () => {
expect(component).toBeTruthy();
});

it(‘should filter cards correctly on ngOnInit’, () => {
const mockCardValues = [
{ value: { actionToggle: ActionType.Buy }, id: 1 },
{ value: { actionToggle: ActionType.Sell }, id: 2 },
{ value: { actionToggle: ActionType.Buy }, id: 3 }
];

```
component.cardValues = mockCardValues;
component.ngOnInit();

expect(component.buyCards).toHaveLength(2);
expect(component.sellCards).toHaveLength(1);
expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith(mockCardValues);
```

});

it(‘should handle empty cardValues’, () => {
component.cardValues = [];
component.ngOnInit();

```
expect(component.buyCards).toEqual([]);
expect(component.sellCards).toEqual([]);
expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith([]);
```

});
});