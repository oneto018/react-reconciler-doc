# react-reconciler-doc
An Incomplete documentation of react-reconciler

**⚠ this is for `react-reconciler` version:0.15.0  and `react` version:16.5.2**

an example implementation and usage of this would be in the repo https://github.com/oneto018/file_renderer_experiment

```javascript

import ReactReconciler from 'react-reconciler';

const rootHostContext = {};

const hostConfig = {

	now: Date.now, 
	getRootHostContext: () => {
		//possibly you could maintain some state for renderer (not the app state) 
		return rootHostContext;
	},
	prepareForCommit: () => {},
	resetAfterCommit: () => {},
	getChildHostContext: () => {},
	shouldSetTextContent: (type, props) => {
		//determining what constitutes a text node
		 return typeof props.children === 'string' || typeof props.children === 'number';;
	},
	createInstance: (type, newProps, rootContainerInstance, _currentHostContext, workInProgress) => {
		//place where you create the elements in renderer
		
		//this would be called at any point when a new instance of an elemnt is encountered and only once per instance of the element

		//regardless whatever you do here, return an object representing the element, since thats what going to referenced here after for manipulating the element
		return createElement(type, newProps, rootContainerInstance, _currentHostContext, workInProgress);
	},
	createTextInstance: (text ) => {
		//do any special case for text nodes otherwise return the text as it is
		return text;
	},
	appendInitialChild:(parent, child) => {
		//renderer will start from deepest child 
		//this would be called for each child during initial render


	},

	finalizeInitialChildren: (element, type, props) => {
		//this would be called once all the children of an elemet is created 

		//would be repeated for all the elements 
		console.log('finalize inital children',{element,type,props});
	},
	supportsMutation: true, //this is configuring that this renderer to support changes after initiated
	appendChildToContainer :(parent, child) =>{
		// called on first render, when children of the root element is being added to the root

		//here the parent would be root container
		console.log('appendChildToContainer',{parent,child});
	},
	prepareUpdate(element, type, oldProps,newProps){
		//this is where  you are supposed to compute diff and return

		//if you return false or any falsy value, there won't be any update for that element or its children
		
		//if you return anything other than a falsy value, you would get that in 2nd argument in commitUpdate

		//I just used a simple object diff library which deeply compares the object.
		//here you could also use some immutable library for efficiency for fast diff
	   console.log('prepareUpdate',{element,type,newProps,df});
	   return diff(oldProps,newProps);;

	},
	commitUpdate(element, updatePayload, type, oldProps, newProps) {
		//here is where you are supposed to apply the update based on the diff you get from updatePayload
		//updatePayload is just whatever returned by prepareUpdate for this element

		console.log('commitUpdate',util.inspect({name,type,updatePayload,type,diffObj},{depth:null,colors:true}));
	},
	commitTextUpdate(textInstance, oldText, newText) {
    console.log('commitTextUpdate',{textInstance,oldText,newText});
  },
  removeChild(parent, child) {
  	console.log('removeChild',{parentInstance,child});
  	//called when there is change in tree and one of the child needs to be removed

  	//implement your logic based on the parent and child
  },

  appendChild: (parent, child) => {
		//called when there is change in tree and one of the child needs to be added

		//implement your logic based on the parent and child
  },
};
const ReactReconcilerInst = ReactReconciler(hostConfig);
export default {
	//public render method for your renderer
	render: (reactElement, domElement, callback) => {
	//kind of like a boilerplate code creating root container if it  hasn't been created otherwise just put the element inside the container
		if (!domElement._rootContainer) {
	      domElement._rootContainer = ReactReconcilerInst.createContainer(domElement, false);
	    }

    // update the root Container
    return ReactReconcilerInst.updateContainer(reactElement, domElement._rootContainer, null, callback);
	}
}

```
At any point in time, do put more console.log to understand the order of operations and more parameters 


For all the possible methods and configuration in react-reconciler 
https://github.com/facebook/react/blob/master/packages/react-reconciler/src/forks/ReactFiberHostConfig.custom.js

