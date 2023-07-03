# Reactive Angular with NgRx

## NgRx Store

**State & Reducer**

The state is any data that's going to be use throughout the application with NgRx it is recommended to create an interface containing the properties of the state and create and object to store the initial state

```ts
interface ProjectsState {
	projects: Project[];
	selectedProjectId: string | null;
}

export const initialState: ProjectsState {
	projects: [],
	selectedProjectId: null,
}
```

Once your state is defined the next step is to build a reducer which are responsible for handling transitions from states, a reducer is just a function that takes two parameters the state and an action that's going to specify how the data should be handled

```ts
export function projectsReducers(state = initialState, action): ProjectsState {
	switch(action.type) {
		default:
			return state;
	}
}
```

NgRx comes with utilities that allows us to combine all of our reducers into a single source of truth reducer

```ts
// import your reducers
import {customersReducer} from 'customers.reducer';
import {projectsReducer} from 'projects.reducer';

// ActionReducerMap and AppState are provided by NgRx
export const reducers: ActionReducerMap<AppState> = {
	customers: customersReducer,
	projects: projectsReducer,
}
```

Now to finish setting up our store we need to add it in the imports array of our module

```ts
@NgModule({
	imports: [
		// StoreModule is imported from NgRx and pass your reducers
		StoreModule.forRoot(reducers),
],
})
export class StateModule {}
```

Now in order to use the store in our components we need to inject it

```ts
@Component({
	// Angular component metadata goes here
})
export class ProjectsComponent implements OnInit {
	projects$: Observable<Project[]>;
	customers$: Observable<Customer[]>;
	currentProject: Project;

	constructor(
		private store: Store<ProjectsState>
	) {}
	
	ngOnInit() {
		// get the state
		this.projects$ = store.pipe(
			select('projects')
		);
	}
}
```

When in your reducer you define actions to be handled the component has to dispatch those actions in order to interact with the reducer

```ts
// method in the component
createdProject(project) {
	this.store.dispatch({type: 'create', payload: project})
}
```

# Actions & Entities

Actions need to be unique NgRx comes with utilities that allows us to create ids for the actions

```ts
export enum ProjectsActionTypes {
	ProjectSelected = '[Projects] Selected',
	AddProject = '[Projects] Add',
}

export class AddProject implements Action {
	readonly type = ProjectsActionTypes.AddProject;
	constructor(private payload: Project) {}
}

export type ProjectsActions = AddProject;
// import these actions to the reducer and add it to the different cases
```

Now that the actions are created they have to be wire to a component

```ts
// method in the component
createProject(project) {
	this.store.dispatch(new AddProject(project));
}
```

**Entities** allows us to manipulate and query entity collections

```ts
export interface ProjectState extends EntityState<Project> {
	selectedProjectId: string | null;
}

// entity adapter
export const adapter: EntityAdapter<Project> = createEntityAdapter<Project>();

export const initialState: ProjectsState = adapter.getInitialState({
	selectedProjectId: null,
});

export function projectsReducers(state = initialState, action): ProjectsState {
	switch(action.type) {
		case ProjectActionTypes.AddProject:
			return adapter.addOne(action.payload, state);
		default:
			return state;
	}
}
```

Now the reducer has been changed to be handled by the entities and adapter set up the entity to the component

```ts
// method in the component
createProject(project) {
	this.store.dispatch(new AddProjects(project));
}	
```

# Selectors & Side Effects

Selectors are pure functions used for obtaining slices of store state

```ts
// selector
export const getSelectedProjectId = (state: ProjectsState)  = state.getSelectedProjectId;

const {selectIds, selectEntities, selectAll} = adapter.getSelectors();

export const selectProjectIds = selectIds;

export const selectProjectState = createFeatureSelector<fromProjects.ProjectState>('projects');

export const selectProjectIds = createSelector(
	selectProjectState,
	fromProjects.selectProjectIds
);
```
Use the selector in the component

```ts
ngOnInit() {
	this.projects$ =  this.store.pipe(select(selectAllProjects));
}
```

**Side effects and server calls**

```ts
@Injectable({})
export class ProjectsEffects {
	@Effect() loadProjects$ = this.dataPersistence.fetch(ProjectsActionTypes.LoadProjects, {
	run: () => {},
	onError: () => {}
})
	constructor(
		private actions$: Actions,
		private dataPersistence: DataPersistence<ProjectState>,
		private projectsService: ProjectsService
	) {}
}
```

We can create an injectable class and use the @Effect decorator provided by NgRx in order to add side effects and persist the data

# Selectors & Facade

The facade pattern allows us to clean our code up and structure it in a better way

```ts
@Injectable({})
export class ProjectsFacade {
	$projects: Observable<Project[]>;
	constructor(private store: Store<ProjectState>) {
		this.projects$ = this.store.pipe(select(selectAllProjects));
	}
}
```

```ts
	// constructor component
	constructor(private facade: ProjectsFacade) {
		this.projects$ = facade.projects$;
	}
```

NgRx is a powerful tool to develop reactive applications, allows us to  define our state and keep track of the actions overtime
