// my.component.spec.ts
import { TestBed } from '@angular/core/testing';
import { Router } from '@angular/router';
import { RouterTestingModule } from '@angular/router/testing';
import { MyComponent } from './my.component';

describe('MyComponent.onEditTradeRequest', () => {
  let component: MyComponent;
  let router: Router;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [RouterTestingModule.withRoutes([])], // provides Router
      declarations: [MyComponent],
    }).compileComponents();

    const fixture = TestBed.createComponent(MyComponent);
    component = fixture.componentInstance;
    router = TestBed.inject(Router);
  });

  it('navigates to /invest/trade-order', () => {
    const spy = jest.spyOn(router, 'navigateByUrl').mockImplementation(() => true as any);

    component.onEditTradeRequest();

    expect(spy).toHaveBeenCalledWith('/invest/trade-order');
  });
});