## Preparation
Developers have to set up all component, services, providers that were used in component file.

That seeems to be service unit test. But a bit different is from configuration. TestBed preparation may be more complex and more step in setting up.
### 1. Start with TestBed
Let' s dive in `.spec.ts` file. At initialization, when component had been created, there are 2 beforeEach for configuring separately.

- <ins>beforeEach</ins> with `async` is the place where all services related components, directives, etc
- Notice to: 
    - declarations - where specific component is going to write against, this import is mandatory 
    - imports - where modules from Angular libraries or third-party libraries. Similarly imports in @NgModule decorator.
    - providers - where developers put component or directive. Here also the place to declare a mock data for test cases.
- <ins>beforeEach</ins> where target component is injected into Testbed. 
Developers can inject other component at this metadata property in case the component was not declared in `providers` (not recommend in this case).

      beforeEach(async () => {
        await TestBed.configureTestingModule({
          declarations: [ TestingComponent ]
        })
      .compileComponents();
      });

      beforeEach(() => {
        fixture = TestBed.createComponent(TestingComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
      });
   
 Run at least 1 test case like below to make sure that all configurations run right before carrying out next step.
 
     it('should be created', () => {
        expect(component).toBeTruthy();
      });
 
 ### 2. Function contains services
 Going to test function with service that injected from constructor.
 
     intervalNotReceive() {
        const param = {
          id: this.userId,
          type: DYNAMO_TYPE.USER
        };
        this.dynamoService
          .getDynamoChange(param)
          .pipe(takeUntil(this.unReceiveDestroy$))
          .subscribe((res: DynamoCheckResponse) => {
            if (res?.results === DYNAMO_STATUS.success && res?.data?.length > 0) {
              this.setNumberOfUnreceivedDistributions();
            }
          });
      }
From .spec.ts file:
  
    describe('Test intervalNotReceive function', () => {
      it('Test function with success data from dynamo', () => {
        const setNumberOfUnreceivedDistributions = spyOn(component, 'setNumberOfUnreceivedDistributions');
        component.intervalNotReceive();
        spyOn(component['dynamoService'], 'getDynamoChange').and.returnValue(of({results: 1, data: [{id: 123}]}));
        expect(setNumberOfUnreceivedDistributions).toHaveBeenCalled();
      })
    });
Return value of the `getDynamoChange` function was mocked in order to go through last expression. 

That can be mock by any return type up to developers's purpose.

Normal function can be mocked by using `spyOn`.

Developers also mock data is declaring return type in `providers`.

    const mockData: Partial<DynamoService>{
        getDynamoChange: () => of({results: 1, data: [{id: 123}]}));
    }
    
Add to providers from TestBed with async:

    providers: [
    
        ...
        
        { provide: DynamoService, useValue: mockData }
    ]

Function `getDynamoChange` will return exactly value that configure in `mockData`.

#### Mock data with different returns
This code block mock a response with 2 different return data.

    describe('Test setNumberOfUnreceivedDistributions function', () => {
        const getNotReceivedCountReturn = {
          results: 1,
          notReceivedCount: 1
        };
        const getNotReceivedCountReturnOtherCase = {
          results: 1,
          notReceivedCount: 10,
          modified: 12235
        };
        it('run with notReceivedCount to be 1', () => {
          const distribute = spyOn(distributionService, 'getNotReceivedCount').and.returnValue(of(getNotReceivedCountReturn))
          component.setNumberOfUnreceivedDistributions();
          expect(distribute).toHaveBeenCalled();
        });
        it('run with notReceivedCount to be greater than 9 other case', () => {
          const distribute = spyOn(distributionService, 'getNotReceivedCount').and.returnValue(of(getNotReceivedCountReturnOtherCase))
          component.setNumberOfUnreceivedDistributions();
          expect(distribute).toHaveBeenCalled();
        });
    });
 
### 3. Common function, cookie, localStorage
#### Common function
`navigateToTermOfUse` is common function was imported before.

    getHelpOptionsLang() {
        this.termOfUseLinkUrl = navigateToTermOfUse(this.cookieService, this.sanitizer);
    }
With common function, just import all and mock

    import * as commonFunction from '../../';
    
    ...
    describe('Test getHelpOptionsLang function', ()=>{
        it('NavigateToTermOfUse and getPrivacyPolicyURL function should be called', ()=>{
          const spyNavigate = jest.spyOn(commonFunction, 'navigateToTermOfUse');
          component.getHelpOptionsLang();
          expect(spyNavigate).toHaveBeenCalled();
        });
    });
#### Cookie
Try to pass a statement and expect to be called:

    describe('Test backToLogin function', ()=>{
        it('Case single sign on to be true', () => {
          component['cookieService'].set('single_sign_on', 'true');
          component.backToLogin();
          expect(component['cookieService'].get).toHaveBeenCalled();
        });
    });
#### LocalStorage
Set directly localStorge

    describe('Test getHelpOptionsLang function', ()=>{
        it('Case PersonalUse', () => {
          const socialAccountType = '5';
          spyOn(component, 'backToLogin');
          localStorage.setItem(SOCIAL_ACCOUNT_TYPE, socialAccountType);
          component.disagree();
          expect(component.backToLogin).toHaveBeenCalled();
        });
    });
### 4. Cover private function
Use `jest.spyOn()`

    describe('メソッドのテスト', () => {
        it('notebookTitleChanged()の実行', () => {
          fixture.detectChanges();
          const spy = jest.spyOn(component, 'privateFunction');
          component.notebookTitleChanged();
          expect(spy).toHaveBeenCalledWith(component.notebookTitle);
        });
      });


### 5. Interval function
Wrap up test function between `jest.useFakeTimers()` and `jest.runOnlyPendingTimers()` to implement interval

    describe('Test intervalNotReceive function', () => {
        it('Test function with success data from dynamo', () => {
          const setNumberOfUnreceivedDistributions = spyOn(component, 'setNumberOfUnreceivedDistributions');
          jest.useFakeTimers();
          component.intervalNotReceive();
          spyOn(component['dynamoService'], 'getDynamoChange').and.returnValue(of({results: 1, data: [{id: 123}]}));
          jest.runOnlyPendingTimers();
          expect(setNumberOfUnreceivedDistributions).toHaveBeenCalled();
        })
      });

### 6. Router
Make sure that imports contains `RouterModule.forRoot([])` and `{ provide: APP_BASE_HREF, useValue: '/' }` to cover router

    describe('Test backToLogin function', ()=>{
        it('Case single sign on to be true', () => {
          spyOn(component['myPageService'], 'getUserInfo')
          spyOn(component['cookieService'], 'get');
          spyOn(component['authen'], 'clearData');
          spyOn(component['router'], 'navigateByUrl')
          component.backToLogin();
          expect(component['myPageService'].getUserInfo).toHaveBeenCalled();
          expect(component['cookieService'].get).toHaveBeenCalled();
          expect(component['router'].navigateByUrl).toHaveBeenCalled();
          expect(component['authen'].clearData).toHaveBeenCalled();
        });
    });
#### Special case
When `.ts`  has a statement to check `router.url`
This is solution:

     RouterTestingModule.withRoutes([
      { path: 'login', component: TermsOfServiceComponent },
     ]),

    beforeEach(fakeAsync(() => {
      const router = TestBed.inject(Router);
      router.initialNavigation();
      router.navigateByUrl('login');
      tick();
    }))
 


 
