## Preparation
Developers have to set up all component, services, providers that were used in service file
### 1. Start with TestBed
Service (within this Angular project) always use `httpClient` to execute requests.

Besides providing component, `HttpClientTestingModule` should be declare.

This set up seems to be simple requirement for simple service

    beforeEach(() => {
        TestBed.configureTestingModule({
          imports: [HttpClientTestingModule],
          providers: [TermsOfServiceService]
        });
        service = TestBed.inject(TestingService);
      });
      
 Of course, when service injected many components or other services even store in Redux flow, developer must add specify data
 
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
    
DataReturn is return of the method, this could be mock as a constant


 
