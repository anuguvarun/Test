import { ComponentFixture, TestBed } from '@angular/core/testing';
import { RequestTradeContainer } from './request-trade.container';

describe('RequestTradeContainer', () => {
  let component: RequestTradeContainer;
  let fixture: ComponentFixture<RequestTradeContainer>;

  const mockTradeRecommendationFacade = {
    setAccountName: jest.fn(),
    setAccountNumber: jest.fn(),
    setHeaderLinkText: jest.fn(),
    setHeaderLinkRoute: jest.fn(),
    setSteps: jest.fn(),
  };

  const mockTradeFacade = {
    setPartyId: jest.fn(),
  };

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [RequestTradeContainer],
      providers: [
        { provide: 'tradeRecommendationFacade', useValue: mockTradeRecommendationFacade },
        { provide: 'tradeFacade', useValue: mockTradeFacade },
      ],
    });

    fixture = TestBed.createComponent(RequestTradeContainer);
    component = fixture.componentInstance;
    // Inject mock facades manually if not using Angular DI tokens
    component['tradeRecommendationFacade'] = mockTradeRecommendationFacade;
    component['tradeFacade'] = mockTradeFacade;
  });

  it('should set accountName', () => {
    component.accountName = 'TestAccount';
    expect(mockTradeRecommendationFacade.setAccountName).toHaveBeenCalledWith('TestAccount');
  });

  it('should set accountNumber', () => {
    component.accountNumber = '123456';
    expect(mockTradeRecommendationFacade.setAccountNumber).toHaveBeenCalledWith('123456');
  });

  it('should set linkText', () => {
    component.linkText = 'Go to dashboard';
    expect(mockTradeRecommendationFacade.setHeaderLinkText).toHaveBeenCalledWith('Go to dashboard');
  });

  it('should set linkStateName', () => {
    component.linkStateName = 'dashboard';
    expect(mockTradeRecommendationFacade.setHeaderLinkRoute).toHaveBeenCalledWith('dashboard');
  });

  it('should set steps', () => {
    const steps = [{ title: 'Step 1' }, { title: 'Step 2' }];
    component.steps = steps;
    expect(mockTradeRecommendationFacade.setSteps).toHaveBeenCalledWith(steps);
  });

  it('should set partyId', () => {
    component.partyId = 42;
    expect(mockTradeFacade.setPartyId).toHaveBeenCalledWith(42);
  });
});