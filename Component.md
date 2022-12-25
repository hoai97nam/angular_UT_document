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
 
 ### 3. Common function, cookie, localStorage
 ### 4. Cover private function
 ### 5. Interval function
 ### 6. Router
 
 
 


 
