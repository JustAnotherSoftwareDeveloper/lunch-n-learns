---
title: "Typescript: A Basic Overview"
theme: moon
---

## Testing in Angular

### A CPA Global Presentation

---

# Overview

- Unit Testing in Javascript
- What is Jest?
- What is ts-mockito
- @ngrx Specific Testing
- Example Tests

---

# Unit Testing in Javascript

---

## A lot like Unit Testing in Java...

- Used to test individual component logic
- Many tests describing different cases
- Run at build time

---

## ...with a few key differences

- Runs headless browser under the hood
- Does not test user input
- Does test data model changes
- Does test method calls

---

## Key Terminology

- Mocks - Replaces object/function with fake implementation
- Spies - Tracks info of object/function
- Stub - Real object/function with some mocked attributes

---

# What is Jest

----

## Jest is a testing framework developed by Facebook

----

## Much faster than alternatives

---

# What is ts-mockito

----

## mocking library inspired by mockito

----

Used to mock injectable services 
```javascript
const mockService = mock(CustomService);
const provider =
{
    provide: CustomService,
    useValue: instance(mockService)
}
```

----

Spoofs method calls
```javascript
when(mockService.method(anything())).thenReturn(true);
when(mockService.method(3)).thenCall(() => {});
when(mockService.method(anyInt())).thenThrow(new Error(""));
```
----
Can be used to verify method calls 
```javascript
verify(mockService.method()).times(10);
verify(mockService.method()).atLeast(1);
verify(mockService.methodOne()).wasCalledBefore(mockServiceTwo.methodTwo());
```

---
# @ngrx Specific Testing

----
Provide Mock Store
```javascript
const initialState = {
    userId: 1;
}
const store: MockStore<AppState>;
const provider = provideMockStore({initialState});
...
mockStore = Testbed.get(Store)
```
----
Provide Mock Actions
```javascript
let actions$: Observable<any>;
const provider = provideMockActions(() => actions$)
```

---
# Example Tests

----
Component 
```javascript
describe('CoreComponent', () => {
  let component: CoreComponent;
  let fixture: ComponentFixture<CoreComponent>;
  let store: MockStore<AppState>;
  let translateMock: TranslateService;
  let logoutServiceMock;
  const translatePipe = new MockTranslationPipe();
  const contentQuery = '[id="main.content"]';
  const initialState: AppState = {
    core: {
      userDetails: {
        loggedInUser: null,
        loaded: false
      },
      translation: {
        loaded: false
      },
      enums: {
        loaded: null,
        enums: null,
        countries: null,
        currencies: null,
        demoDataOptions: null
      }
    },
    router: null,
    'cost-center': {
      organizations: {
        loaded: true,
        selectedOrg: {
          name: 'test',
          guk: '1234',
          existsInSystem: true
        },
        organizations: []
      },
      costCenterTree: {
        currentCCLogo: null,
        organizationTrees: {},
        currentCC: {
          name: 'test',
          children: [],
          path: '',
          id: -1,
          fullPath: ''
        }
      }
    },
    dashboard: null,
    help: null
  };
  // tslint:disable-next-line: prefer-const
  let actions$: Observable<Action>;
  beforeEach(async(() => {
    translateMock = mock(TranslateService);
    when(translateMock.onTranslationChange).thenReturn(
      new EventEmitter<TranslationChangeEvent>()
    );
    logoutServiceMock = mock(LogoutService);
    TestBed.configureTestingModule({
      imports: [
        RouterTestingModule,
        StoreModule.forRoot(reducers),
        FakesModule
      ],
      providers: [
        provideMockStore({ initialState }),
        {
          provide: LogoutService,
          useValue: instance(logoutServiceMock)
        },
        {
          provide: TranslateService,
          useValue: instance(translateMock)
        }
      ],
      declarations: [CoreComponent]
    }).compileComponents();
  }));

  beforeEach(() => {
    store = TestBed.get(Store);
    spyOn(store, 'dispatch');
    fixture = TestBed.createComponent(CoreComponent);
    component = fixture.componentInstance;
    fixture.detectChanges();
    when(translateMock.get(anything(), anything())).thenCall(args =>
      of(translatePipe.transform(args))
    );
    when(
      translateMock.getParsedResult(anything(), anything(), anything())
    ).thenCall(args => translatePipe.transform(args[0]));
  });

  it('should create', () => {
    expect(component).toBeTruthy();
  });

  it('Should call Get User', () => {
    expect(store.dispatch).toHaveBeenCalledWith(
      UserDetailActions.GET_OR_LOAD({})
    );
  });
  it('Should call Load Orgs', () => {
    expect(store.dispatch).toHaveBeenCalledWith(
      OrganizationsActions.LOAD_ORGANIZATIONS({})
    );
  });
  it('Should call Get Translations', () => {
    expect(store.dispatch).toHaveBeenCalledWith(
      TranslationActions.GET_BASE({})
    );
  });
  it('Should Call Get Enums', () => {
    expect(store.dispatch).toHaveBeenCalledWith(EnumsActions.GET_OR_LOAD({}));
  });
  it('Should Call Get Countries', () => {
    expect(store.dispatch).toHaveBeenCalledWith(
      EnumsActions.GET_OR_LOAD_COUNTRIES({})
    );
  });
  it('Should Call Get Currencies', () => {
    expect(store.dispatch).toHaveBeenCalledWith(
      EnumsActions.GET_OR_LOAD_CURRENCIES({})
    );
  });
  it('Should not be visible when nothing is loaded', () => {
    expect(
      (fixture.debugElement.nativeElement as HTMLElement).querySelector(
        contentQuery
      )
    ).toBeFalsy();
  });
  it('Should set default language', () => {
    const newState: AppState = {
      ...initialState,
      core: {
        ...initialState.core,
        userDetails: {
          loaded: true,
          loggedInUser: {
            userLanguage: 'en-us',
            dateFormatPref: 'YYYY-MM-DD',
            defaultScenarioDateRange: 'TEN_FISCAL_YEARS',
            itemsPerPage: 50,
            userId: -1,
            links: [],
            permissions: [],
            superuser: true
          }
        }
      }
    };
    store.setState(newState);
    fixture.detectChanges();
    verify(translateMock.setDefaultLang('en-us')).called();
  });
  it('Should not be visible when only user details are loaded', () => {
    store.setState({
      ...initialState,
      core: {
        userDetails: {
          loaded: true
        }
      }
    } as any);
    fixture.detectChanges();
    expect(
      (fixture.debugElement.nativeElement as HTMLElement).querySelector(
        contentQuery
      )
    ).toBeFalsy();
  });
  it('Should not be visible when only translations are loaded', () => {
    store.setState({
      ...initialState,
      core: {
        translation: {
          loaded: true
        }
      }
    } as any);
    fixture.detectChanges();
    expect(
      (fixture.debugElement.nativeElement as HTMLElement).querySelector(
        contentQuery
      )
    ).toBeFalsy();
  });
  it('Should be visible when users and translations are loaded', () => {
    store.setState({
      ...initialState,
      core: {
        userDetails: {
          loaded: true
        },
        translation: {
          loaded: true
        }
      }
    } as any);
    fixture.detectChanges();
    expect(
      (fixture.debugElement.nativeElement as HTMLElement).querySelector(
        contentQuery
      )
    ).toBeTruthy();
  });

  it('Should Call into Logout Service', () => {
    when(logoutServiceMock.logout()).thenCall(() => {});
    component.logoutService.logout();
    verify(logoutServiceMock.logout()).called();
  });
});
```

----
Effects
```javascript
import { TestBed } from '@angular/core/testing';
import { provideMockActions } from '@ngrx/effects/testing';
import { Observable, of } from 'rxjs';

import { CostCenterTreeEffects } from './cost-center-tree.effects';
import { provideMockStore } from '@ngrx/store/testing';
import { CostCenterService } from 'src/app/common/service/cost-center.service';
import { mock, instance, when, verify, anything } from 'ts-mockito';
import { Store } from '@ngrx/store';
import { AppState } from '../../reducers';
import cookies from 'js-cookie';
import { CostCenterTree } from '@model/cost-center-tree';
import { CostCenterTreeActions, OrganizationsActions } from '../actions';
import { Organization } from '@model/organization';
import { EMPTY } from '../../reducers/base.reducer';
import { NewCostCenterData } from '@model/new-cost-center-data';
import { DemoDataOption } from '@model/demo-data-option';
import { RouterTestingModule } from '@angular/router/testing';
import { Router } from '@angular/router';
describe('CostCenterTreeEffects', () => {
  // tslint:disable-next-line: prefer-const
  let actions$: Observable<any>;
  let effects: CostCenterTreeEffects;
  let store: Store<AppState>;
  let mockCostCenterService: CostCenterService;
  let router: Router;
  const initialState: AppState = {
    router: null,
    core: null,
    'cost-center': {
      costCenterTree: null,
      organizations: {
        organizations: [],
        selectedOrg: {
          guk: 'abcd',
          existsInSystem: true,
          name: 'testOrg'
        },
        loaded: true
      }
    },
    help: null,
    dashboard: null
  };
  beforeEach(() => {
    mockCostCenterService = mock(CostCenterService);
    TestBed.configureTestingModule({
      imports: [RouterTestingModule],
      providers: [
        CostCenterTreeEffects,
        provideMockActions(() => actions$),
        provideMockStore(),
        {
          provide: CostCenterService,
          useValue: instance(mockCostCenterService)
        }
      ]
    });

    effects = TestBed.get<CostCenterTreeEffects>(CostCenterTreeEffects);
    store = TestBed.get<Store<AppState>>(Store);
    router = TestBed.get<Router>(Router);
  });

  it('should be created', () => {
    expect(effects).toBeTruthy();
  });

  describe('When Org is Selected, Select Cost Center', () => {
    const tree: CostCenterTree[] = [
      {
        name: 'One',
        id: 1,
        path: '',
        fullPath: '1',
        children: []
      },
      {
        name: 'Two',
        id: 2,
        path: '',
        fullPath: '2',
        children: [
          {
            name: 'Three',
            id: 3,
            path: '3',
            fullPath: '2,3',
            children: []
          },
          {
            name: 'Four',
            id: 4,
            fullPath: '2,4',
            path: '4',
            children: [
              {
                name: 'Five',
                id: 5,
                fullPath: '2,4,5',
                path: '4,5',
                children: []
              }
            ]
          }
        ]
      }
    ];
    it('Select First CC When No Cost Center is Specified', () => {
      const testAction = CostCenterTreeActions.TREE_FOR_ORGANIZATION_LOADED({
        tree,
        guk: '1234'
      });
      actions$ = of(testAction);
      const cc = tree[0];
      actions$.subscribe(action => {
        expect(action).toEqual(
          CostCenterTreeActions.COST_CENTER_SELECTED({ cc })
        );
      });
    });
    it('Select CC When Path is Specified', () => {
      const testAction = CostCenterTreeActions.TREE_FOR_ORGANIZATION_LOADED({
        tree,
        guk: '1234'
      });
      actions$ = of(testAction);
      const cc = tree[1].children[1].children[0];
      cookies.set('selected_cc_path', encodeURI(cc.fullPath));
      actions$.subscribe(action => {
        expect(action).toEqual(
          CostCenterTreeActions.COST_CENTER_SELECTED({ cc })
        );
      });
    });
    it('Select First CC When Path is specified, but not found', () => {
      cookies.set('selected_cc_path', encodeURI('2,3,4,6'));
      const testAction = CostCenterTreeActions.TREE_FOR_ORGANIZATION_LOADED({
        tree,
        guk: '1234'
      });
      actions$ = of(testAction);
      const cc = tree[0];
      actions$.subscribe(action => {
        expect(action).toEqual(
          CostCenterTreeActions.COST_CENTER_SELECTED({ cc })
        );
      });
    });
  });
  it('Selecting an Organization Loads the Org Tree', () => {
    const org: Organization = {
      name: 'Test',
      guk: 't1234',
      existsInSystem: true
    };
    actions$ = of(
      OrganizationsActions.ORGANIZATION_SELECTED({ organization: org })
    );
    actions$.subscribe(action => {
      expect(action).toEqual(
        CostCenterTreeActions.LOAD_TREE_FOR_ORGANIZATION({ guk: '1234' })
      );
    });
  });

  describe('Getting Tree or Loading it From Cache', () => {
    it('When CC is not in Store, Get Tree', () => {
      const act = CostCenterTreeActions.LOAD_TREE_FOR_ORGANIZATION({
        guk: 't1234'
      });
      actions$ = of(act);
      actions$.subscribe(action => {
        expect(action).toEqual(
          CostCenterTreeActions.GET_TREE_FOR_ORGANIZATION({ guk: 't1234' })
        );
      });
    });
    it('When CC is in Store, load tree immediately', () => {
      const ccTree: CostCenterTree[] = [
        {
          name: 't',
          id: 1,
          path: '',
          fullPath: '1',
          children: []
        }
      ];
      store.dispatch(
        CostCenterTreeActions.TREE_FOR_ORGANIZATION_LOADED({
          tree: ccTree,
          guk: 't1234'
        })
      );
      const act = CostCenterTreeActions.LOAD_TREE_FOR_ORGANIZATION({
        guk: 't1234'
      });
      actions$ = of(act);
      actions$.subscribe(action => {
        expect(action).toEqual(
          CostCenterTreeActions.TREE_FOR_ORGANIZATION_LOADED({
            guk: 't1234',
            tree: ccTree
          })
        );
      });
    });
    it('When Get Tree is Called, get from WS then call loaded', () => {
      const act = CostCenterTreeActions.LOAD_TREE_FOR_ORGANIZATION({
        guk: 't1234'
      });
      actions$ = of(act);
      actions$.subscribe(action => {
        expect(action).toEqual(
          CostCenterTreeActions.GET_TREE_FOR_ORGANIZATION({
            guk: 't1234'
          })
        );
      });
    });
  });
  it('Sets Cookies correctly When CC is selected', () => {
    const cc: CostCenterTree = {
      id: 7,
      name: 'Test',
      children: [],
      path: '1,2,3,4,5',
      fullPath: '1,2,3,4,5,7'
    };
    const act = CostCenterTreeActions.COST_CENTER_SELECTED({ cc });
    actions$ = of(act);
    actions$.subscribe(action => {
      expect(action).toEqual(EMPTY({}));
      expect(cookies.get('selcted_cc_path')).toEqual(encodeURI('1,2,3,4,5,7'));
    });
  });
  it('Should Add New CC', () => {
    const newCC: NewCostCenterData = {
      name: 'test',
      path: '1,2,3,4',
      organization: {
        guk: 'abcd'
      }
    };
    when(mockCostCenterService.addNewCostCenter(newCC)).thenReturn(
      of({ guk: 'abcd', ccId: 1 } as any)
    );
    actions$ = of(
      CostCenterTreeActions.SAVE_COST_CENTER({ cc: newCC, options: null })
    );
    actions$.subscribe(action => {
      expect(action).toEqual(
        CostCenterTreeActions.GET_TREE_FOR_ORGANIZATION({ guk: 'abcd' })
      );
      verify(mockCostCenterService.addNewCostCenter(newCC)).once();
    });
  });
  it('Should Kick Off Populate When Demo Data Options are Loaded', () => {
    const newCC: NewCostCenterData = {
      name: 'test',
      path: '1,2,3,4',
      organization: {
        guk: 'abcd'
      }
    };
    const demoOption: DemoDataOption = { id: 1, description: 'Full' };
    const ccTree: CostCenterTree = {
      id: 1,
      path: '',
      fullPath: '1',
      children: [],
      name: 'test'
    };
    when(mockCostCenterService.addNewCostCenter(newCC)).thenReturn(of(ccTree));
    actions$ = of(
      CostCenterTreeActions.SAVE_COST_CENTER({
        cc: newCC,
        options: demoOption
      })
    );
    actions$.subscribe(action => {
      expect(action).toEqual(
        CostCenterTreeActions.POPULATE_CC_DATA({
          cc: ccTree,
          options: demoOption
        })
      );
      verify(mockCostCenterService.addNewCostCenter(newCC)).once();
    });
  });

  it('Get CC Logo Should Kickoff Loaded CC Logo', () => {
    when(mockCostCenterService.getLogoImage(anything())).thenReturn(of('uri'));
    actions$ = of(CostCenterTreeActions.GET_CC_LOGO({ id: 1 }));
    actions$.subscribe(action => {
      expect(action).toEqual(
        CostCenterTreeActions.CC_LOGO_LOADED({ logo: 'uri' })
      );
      verify(mockCostCenterService.getLogoImage(1)).once();
    });
  });
  it('Populate CC Data should call get tree afterwards', () => {
    const demoOption: DemoDataOption = { id: 1, description: 'Full' };
    const ccTree: CostCenterTree = {
      id: 1,
      path: '',
      fullPath: '1',
      children: [],
      name: 'test'
    };
    when(
      mockCostCenterService.populateCCData(anything(), anything())
    ).thenReturn(of());
    actions$ = of(
      CostCenterTreeActions.POPULATE_CC_DATA({
        cc: ccTree,
        options: demoOption
      })
    );
    actions$.subscribe(action => {
      verify(mockCostCenterService.populateCCData(1, demoOption)).once();
      expect(action).toEqual(
        CostCenterTreeActions.GET_TREE_FOR_ORGANIZATION({ guk: 'abcd' })
      );
    });
  });
  it('Changing Cost Center Should Cause Router To Navigate', () => {
    const ccTree: CostCenterTree = {
      id: 1,
      path: '',
      fullPath: '1',
      children: [],
      name: 'test'
    };
    actions$ = of(CostCenterTreeActions.COST_CENTER_SELECTED({ cc: ccTree }));
    actions$.subscribe(action => {
      expect(router.navigate).toBeCalledWith(['.'], {
        queryParams: {
          ccId: 1
        }
      });
    });
  });
});

```
----
Reducers
```javascript
import { reducer, initialState } from './cost-center-tree.reducer';
import { CostCenterTreeActions } from '../actions';
import { CostCenterTree } from '@model/cost-center-tree';
import { NewCostCenterData } from '@model/new-cost-center-data';
import { CostCenterState } from '../selectors';
import { CostCenterTreeState } from '../selectors/cost-center-tree.selector';

describe('CostCenterTreeReducer', () => {
  it('should return the previous state', () => {
    const action = {} as any;

    const result = reducer(initialState, action);

    expect(result).toBe(initialState);
  });
  it('Get Tree Should Not Modify State', () => {
    const action = CostCenterTreeActions.GET_TREE_FOR_ORGANIZATION({
      guk: 't1234'
    });
    const result = reducer(initialState, action);
    expect(result).toEqual(initialState);
  });
  it('Load Tree Should Not Modify State', () => {
    const action = CostCenterTreeActions.LOAD_TREE_FOR_ORGANIZATION({
      guk: 't1234'
    });
    const result = reducer(initialState, action);
    expect(result).toEqual(initialState);
  });
  it('Load Logo Should Not Modify State', () => {
    const action = CostCenterTreeActions.GET_CC_LOGO({ id: 1 });
    const result = reducer(initialState, action);
    expect(result).toEqual(initialState);
  });
  it('Logo Loaded Should Set Logo', () => {
    const action = CostCenterTreeActions.CC_LOGO_LOADED({ logo: 'testURI' });
    const result = reducer(initialState, action);
    expect(result.currentCCLogo).toEqual('testURI');
  });
  it('Save Action should delete org heirarchy', () => {
    const state: CostCenterTreeState = {
      currentCC: null,
      organizationTrees: {
        abcd: []
      },
      currentCCLogo: null
    };
    const expectedState: CostCenterTreeState = {
      ...state,
      organizationTrees: {
        abcd: null
      }
    };
    const action = CostCenterTreeActions.SAVE_COST_CENTER({
      cc: {
        name: 't',
        organization: {
          guk: 'abcd'
        },
        path: ''
      },
      options: null
    });
    const result = reducer(state, action);
    expect(result).toEqual(expectedState);
  });
  it('Cost Center Tree Should Set Current CC', () => {
    const cc: CostCenterTree = {
      name: 'test',
      id: -1,
      path: '',
      fullPath: '1',
      children: []
    };
    const action = CostCenterTreeActions.COST_CENTER_SELECTED({ cc });
    const result = reducer(initialState, action);
    expect(result.currentCC).toEqual(cc);
  });
  describe('Tree For Organizations Loaded', () => {
    it('Should Load cc tree into org tree', () => {
      const action = CostCenterTreeActions.TREE_FOR_ORGANIZATION_LOADED({
        guk: '1234',
        tree: []
      });
      const result = reducer(initialState, action);
      expect(result.organizationTrees['1234']).toBeTruthy();
    });
    it('Should not override existing guks in tree', () => {
      const action = CostCenterTreeActions.TREE_FOR_ORGANIZATION_LOADED({
        guk: '1234',
        tree: []
      });
      const result = reducer(initialState, action);
      const action2 = CostCenterTreeActions.TREE_FOR_ORGANIZATION_LOADED({
        guk: '5678',
        tree: []
      });
      const result2 = reducer(result, action2);
      expect(result2.organizationTrees['1234']).toBeTruthy();
      expect(result2.organizationTrees['5678']).toBeTruthy();
    });
  });
});

```

---

# Questions?