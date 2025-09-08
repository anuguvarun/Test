import { TestBed } from '@angular/core/testing';
import { Router } from '@angular/router';
import { MyComponent } from './my.component';

describe('MyComponent.onEditTradeRequest', () => {
  let component: MyComponent;
  let routerMock: { navigateByUrl: jest.Mock };

  beforeEach(async () => {
    routerMock = { navigateByUrl: jest.fn() };

    await TestBed.configureTestingModule({
      declarations: [MyComponent],
      providers: [{ provide: Router, useValue: routerMock }], // injects mock
    }).compileComponents();

    const fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;
  });

  it('navigates to /invest/trade-order', () => {
    component.onEditTradeRequest();
    expect(routerMock.navigateByUrl).toHaveBeenCalledWith('/invest/trade-order');
  });
});