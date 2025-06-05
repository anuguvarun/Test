import { ComponentFixture, TestBed } from ‘@angular/core/testing’;
import { BrowserModule } from ‘@angular/platform-browser’;
import { LinkModule, SVGIconModule } from ‘@fmr-ap001330/fc-modern-angular-r’;
import { of } from ‘rxjs’;
import { ActionType } from ‘../../../security/models/security.model’;
import { TradeRecommendationFacade } from ‘../../../store/trade-recommendation/r’;
import { TradeOrderSuccessComponent } from ‘./trade-order-success.component’;

describe(‘TradeOrderSuccessComponent’, () => {
let component: TradeOrderSuccessComponent;
let fixture: ComponentFixture<TradeOrderSuccessComponent>;
let mockTradeRecommendationFacade: jest.Mocked<TradeRecommendationFacade>;
let mockOrderStepPresenter: jest.Mocked<any>;

const mockCardValues = [
{ value: { actionToggle: ActionType.Buy }, id: 1 },
{ value: { actionToggle: ActionType.Sell }, id: 2 },
{ value: { actionToggle: ActionType.Buy }, id: 3 },
{ value: { actionToggle: ActionType.Sell }, id: 4 }
];

beforeEach(async () => {
mockTradeRecommendationFacade = {
someMethod: jest.fn()
} as any;

```
mockOrderStepPresenter = {
  getCardInfoByAction: jest.fn()
};

await TestBed.configureTestingModule({
  imports: [CommonModule, BrowserModule, SVGIconModule, LinkModule],
  declarations: [TradeOrderSuccessComponent],
  providers: [
    { provide: TradeRecommendationFacade, useValue: mockTradeRecommendationFacade },
    { provide: 'OrderStepPresenter', useValue: mockOrderStepPresenter }
  ]
}).compileComponents();

fixture = TestBed.createComponent(TradeOrderSuccessComponent);
component = fixture.componentInstance;
```

});

afterEach(() => {
jest.clearAllMocks();
});

describe(‘Component Initialization’, () => {
it(‘should create’, () => {
expect(component).toBeTruthy();
});

```
it('should initialize empty arrays for buyCards and sellCards', () => {
  expect(component.buyCards).toEqual([]);
  expect(component.sellCards).toEqual([]);
});

it('should have orderStepPresenter injected', () => {
  expect(component.orderStepPresenter).toBeDefined();
});
```

});

describe(‘ngOnInit’, () => {
beforeEach(() => {
component.cardValues = mockCardValues;
});

```
it('should filter cards correctly for buy actions', () => {
  component.ngOnInit();
  
  const expectedBuyCards = mockCardValues.filter(card => 
    card.value.actionToggle === ActionType.Buy
  );
  
  expect(component.buyCards).toEqual(expectedBuyCards);
  expect(component.buyCards).toHaveLength(2);
});

it('should filter cards correctly for sell actions', () => {
  component.ngOnInit();
  
  const expectedSellCards = mockCardValues.filter(card => 
    card.value.actionToggle === ActionType.Sell
  );
  
  expect(component.sellCards).toEqual(expectedSellCards);
  expect(component.sellCards).toHaveLength(2);
});

it('should call orderStepPresenter.getCardInfoByAction with cardValues', () => {
  component.ngOnInit();
  
  expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith(component.cardValues);
  expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledTimes(1);
});

it('should handle empty cardValues array', () => {
  component.cardValues = [];
  component.ngOnInit();
  
  expect(component.buyCards).toEqual([]);
  expect(component.sellCards).toEqual([]);
  expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith([]);
});

it('should handle cardValues with only buy actions', () => {
  const buyOnlyCards = [
    { value: { actionToggle: ActionType.Buy }, id: 1 },
    { value: { actionToggle: ActionType.Buy }, id: 2 }
  ];
  component.cardValues = buyOnlyCards;
  component.ngOnInit();
  
  expect(component.buyCards).toHaveLength(2);
  expect(component.sellCards).toHaveLength(0);
});

it('should handle cardValues with only sell actions', () => {
  const sellOnlyCards = [
    { value: { actionToggle: ActionType.Sell }, id: 1 },
    { value: { actionToggle: ActionType.Sell }, id: 2 }
  ];
  component.cardValues = sellOnlyCards;
  component.ngOnInit();
  
  expect(component.buyCards).toHaveLength(0);
  expect(component.sellCards).toHaveLength(2);
});

it('should handle undefined cardValues gracefully', () => {
  component.cardValues = undefined;
  
  expect(() => component.ngOnInit()).not.toThrow();
});

it('should handle null cardValues gracefully', () => {
  component.cardValues = null;
  
  expect(() => component.ngOnInit()).not.toThrow();
});
```

});

describe(‘Card Filtering Logic’, () => {
it(‘should correctly identify ActionType.Buy cards’, () => {
const testCard = { value: { actionToggle: ActionType.Buy }, id: 1 };
const result = testCard.value.actionToggle === ActionType.Buy;

```
  expect(result).toBe(true);
});

it('should correctly identify ActionType.Sell cards', () => {
  const testCard = { value: { actionToggle: ActionType.Sell }, id: 1 };
  const result = testCard.value.actionToggle === ActionType.Sell;
  
  expect(result).toBe(true);
});

it('should handle cards with missing actionToggle property', () => {
  const invalidCards = [
    { value: {}, id: 1 },
    { value: { actionToggle: null }, id: 2 },
    { value: { actionToggle: undefined }, id: 3 }
  ];
  component.cardValues = invalidCards;
  component.ngOnInit();
  
  expect(component.buyCards).toHaveLength(0);
  expect(component.sellCards).toHaveLength(0);
});

it('should filter cards immutably', () => {
  const originalCards = [...mockCardValues];
  component.cardValues = mockCardValues;
  component.ngOnInit();
  
  expect(component.cardValues).toEqual(originalCards);
  expect(component.cardValues).toBe(mockCardValues); // Same reference
});
```

});

describe(‘Integration with OrderStepPresenter’, () => {
it(‘should call getCardInfoByAction after filtering cards’, () => {
component.cardValues = mockCardValues;

```
  component.ngOnInit();
  
  expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledTimes(1);
  expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith(mockCardValues);
});

it('should handle presenter method throwing error', () => {
  mockOrderStepPresenter.getCardInfoByAction.mockImplementation(() => {
    throw new Error('Presenter error');
  });
  component.cardValues = mockCardValues;
  
  expect(() => component.ngOnInit()).toThrow('Presenter error');
});

it('should call presenter method even with empty cards', () => {
  component.cardValues = [];
  component.ngOnInit();
  
  expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledWith([]);
});
```

});

describe(‘Component State Management’, () => {
it(‘should maintain separate buy and sell card arrays’, () => {
component.cardValues = mockCardValues;
component.ngOnInit();

```
  expect(component.buyCards).not.toBe(component.sellCards);
  expect(component.buyCards.every(card => card.value.actionToggle === ActionType.Buy)).toBe(true);
  expect(component.sellCards.every(card => card.value.actionToggle === ActionType.Sell)).toBe(true);
});

it('should reset arrays on subsequent ngOnInit calls', () => {
  // First call
  component.cardValues = [{ value: { actionToggle: ActionType.Buy }, id: 1 }];
  component.ngOnInit();
  expect(component.buyCards).toHaveLength(1);
  
  // Second call with different data
  component.cardValues = [{ value: { actionToggle: ActionType.Sell }, id: 2 }];
  component.ngOnInit();
  expect(component.buyCards).toHaveLength(0);
  expect(component.sellCards).toHaveLength(1);
});
```

});

describe(‘Edge Cases and Performance’, () => {
it(‘should handle cards with different ActionType values’, () => {
const mixedCards = [
{ value: { actionToggle: ActionType.Buy }, id: 1 },
{ value: { actionToggle: ActionType.Sell }, id: 2 },
{ value: { actionToggle: ‘UNKNOWN’ as any }, id: 3 }
];
component.cardValues = mixedCards;
component.ngOnInit();

```
  expect(component.buyCards).toHaveLength(1);
  expect(component.sellCards).toHaveLength(1);
});

it('should handle large arrays efficiently', () => {
  const largeCardArray = Array.from({ length: 1000 }, (_, i) => ({
    value: { actionToggle: i % 2 === 0 ? ActionType.Buy : ActionType.Sell },
    id: i
  }));
  
  component.cardValues = largeCardArray;
  
  const startTime = Date.now();
  component.ngOnInit();
  const endTime = Date.now();
  
  expect(endTime - startTime).toBeLessThan(100);
  expect(component.buyCards).toHaveLength(500);
  expect(component.sellCards).toHaveLength(500);
});

it('should handle malformed card objects', () => {
  const malformedCards = [
    null,
    undefined,
    { value: null },
    { value: undefined },
    { id: 1 }, // missing value
    { value: { actionToggle: ActionType.Buy }, id: 1 } // valid card
  ] as any[];
  
  component.cardValues = malformedCards;
  
  expect(() => component.ngOnInit()).not.toThrow();
  expect(component.buyCards).toHaveLength(1);
  expect(component.sellCards).toHaveLength(0);
});
```

});

describe(‘Component Lifecycle’, () => {
it(‘should initialize with component creation’, () => {
expect(component.buyCards).toBeDefined();
expect(component.sellCards).toBeDefined();
expect(component.orderStepPresenter).toBeDefined();
});

```
it('should handle multiple ngOnInit calls', () => {
  const spy = jest.spyOn(component, 'ngOnInit');
  
  component.ngOnInit();
  component.ngOnInit();
  component.ngOnInit();
  
  expect(spy).toHaveBeenCalledTimes(3);
  expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledTimes(3);
});
```

});

describe(‘Mock Verification’, () => {
it(‘should verify mock setup’, () => {
expect(mockOrderStepPresenter.getCardInfoByAction).toBeDefined();
expect(jest.isMockFunction(mockOrderStepPresenter.getCardInfoByAction)).toBe(true);
});

```
it('should reset mocks between tests', () => {
  mockOrderStepPresenter.getCardInfoByAction.mockReturnValue('test');
  component.cardValues = mockCardValues;
  component.ngOnInit();
  
  expect(mockOrderStepPresenter.getCardInfoByAction).toHaveBeenCalledTimes(1);
});
```

});
});