## Preparation
Developers have to set up all component, services, providers that were used in service file
### 1. Start with TestBed
Service (within this Angular project) always use `httpClient` to execute requests.

Besides providing component, `HttpClientTestingModule` should be declared.

This set up seems to be simple requirement for simple service

    beforeEach(() => {
        TestBed.configureTestingModule({
          imports: [HttpClientTestingModule],
          providers: [TermsOfServiceService]
        });
        service = TestBed.inject(TestingService);
      });
      
 Of course, when service injected many components or other services even store in Redux flow, developer must add specify data
 
 Run at least 1 test case like below to make sure that all configurations run right.
 
     it('should be created', () => {
        expect(service).toBeTruthy();
      });
 
 ### 2. Service methods
 
 This is a method from service:
 
    view(): Observable<DataModel> {
        return this.http.post<DataModel>(`${apiUrl}`, {});
    }
Corresponding to above method, there is a test:

    it('view method', () => {
      service.view().subscribe({
        next: data => expect(data)
          .withContext('should return Data Info')
          .toEqual(dataReturn),
        error: fail
      })
    })
    
DataReturn is return of the method, this could be mock as a constant.

### 3. Most case
Most cases in service also appear in component, so concentrade to [component](https://github.com/hoai97nam/angular_UT_document/blob/main/Service.md).
