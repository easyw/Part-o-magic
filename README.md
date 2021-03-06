# Part-o-magic
PoM is an experimental add-on module for FreeCAD v0.17. (It won't work with FC v0.16.) 

PoM's main goals are:

* Usability improvements of Part and Body containers, to simplify working with projects with 
multiple parts with deep hierarchy. This includes automatic object addition to active 
container, visibility automation, and other.

* Expand PartDesign-like workflow to Part workbench, and all other workbenches centered around 
making shapes. Module container is the Body-like thing for Part workbench.

Part-o-magic does not provide assembly features, such as constraints. It is aimed at in-place modeling, but it is supposed to be useful in conjunction with assembly.

Beware. Part-o-magic is an epic hack. It will collide with similar functionality in FreeCAD as it is introduced. In case of doubt, you can always switch to Part-o-magic workbench, and disable Observer.

![figure](https://raw.githubusercontent.com/wiki/DeepSOIC/Part-o-magic/pictures/rotating-plate.png)

# Install
Launch FreeCAD. In menu, pick Tools->Addon Manager. Select Part-o-magic in the list, and click Install. Restart FreeCAD.

After restart, you should notice: 
* Part-o-magic workbench should appear in workbench selector. 
* A small global toolbar should appear, which you can disable or customize in Tools->Customize->Toolbars. 
* Behavior of PartDesign workbench should change drastically, that is due to Observer running.

# Uninstall

You can disable PoM Observer, as well as the whole workbench, using buttons on the workbench. If you disable the workbench, all the automation stuff should switch off, but features of part-o-magic can still be recomputed in your projects that have them.

If you completely uninstall the workbench (delete Mod/Part-o-magic folder), part-o-magic features you used in your projects will stop working.

Important. Part-o-magic messes with "DisplayModeBody" properties of PartDesign Body objects. If you uninstall Part-o-magic, or disable its automation stuff, it will cause somewhat unusual behavior of any projects that were saved with part-o-magic enabled and had any container objects present. You can reset them manually with property editor, or run this simple snippet in Py console:

    for obj in App.ActiveDocument.Objects:
        if hasattr(obj.ViewObject, "DisplayModeBody"):
            obj.ViewObject.DisplayModeBody = "Through"
        if hasattr(obj.ViewObject, "Selectable"):
            obj.ViewObject.Selectable = True

# Main features

## "Observer"

### Active container everywhere
In PartDesign, all new features are automatically added to Body. Observer expands this to all workbenches of FreeCAD: new objects, made in any workbench, are automatically added to active container. 

That works (well, it should) in absolutely every workbench, including add-on workbenches and macros!

### Visibility automation
when you activate a container, the container is switched into Through mode, so you see individual contained objects. When you leave it, you see only the final shape (Tip), but not contained objects. Also, when you activate a container, anything outside of it is automatically hidden, so that you can focus on editing the piece.

### Tree automation
when you activate a container, it is automatically expanded in tree. When deactivated, it is automatically collapsed. With the aim to show you only the features that make up the container, so that you can focus on editing the piece.

### Editing automation
When you try to edit a feature, PoM will check if the container of the feature is active, and activate it. (as of now, it has to cancel the editing, so please invoke the edititng again). If the right container is not activated, you may not even see the feature.

## Containers

### Module container
It's an analog of PartDesign Body, but for Part and other workbenches. It groups together features that were used to create a final shape, and exposes the final shape (Tip) as its own. 

When you enter module, you can edit it - add new features from any workbench, including PartDesign Bodies, in order to arrive to the final result shape (typically a solid, but it can be any other type of b-rep shape). When you leave Module, you see Module as the final result. The final result is shape copied from Tip object. Tip object can be assigned with Set Tip tool in PoM.

### ShapeGroup container 
ShapeGroup similar to Module, but it can expose multiple objects to the outside. 

It is somewhat similar to what is called a "group" in vector graphics software. You can group up existing objects, and enter a group to modify its contents (as well as add new objects).

It also can do an operation between objects to be exposed, for example fusing them together into one.

### PartDesign Additive shape, PartDesign Subtractive shape containers
These are just like Modules, but they integrate themselves as PartDesign features. They allow to integrate other workbench tools into PartDesign workflow.

### Ghost
Ghost is a placement-aware version of shapebinder. It is a tool to bring a copy of a shape from one container into another, applying a proper transform.

Ghost supports both extracting an object from a container, and importing an object from a higher-level container. The latter is somewhat limited: it will not work properly if Placement of the container the Ghost is in, changes as a result of a recompute (for example if there is an expression bound to the placement).

### Morph container tool
Created a Module, but later realized you want ShapeGroup instead? Of course, you can create a new container, drag-drop stuff... The "Morph container" tool is for simplifying the process. It takes care of moving stuff, redirecting links, and deletion of the remaining old empty container.

## Other features

* Set Tip. Works on PartDesign Bodies and Module-like things.

* Enter and Leave. Work on almost everything (can be used to enter/leave containers, and edit objects (e.g. open a sketch)).

* Exporter feature, for keeping exported files up to date with project.

* an advanced Object Replacement tool, with container support, and UI to pick specific replacements.

* X-ray tool. For getting through objects to select concealed objects.

* Align View, a one-button replacement for standard view buttons.

# Should I use PoM?

If you are into FreeCAD projects with multiple parts, you should definitely try out Part-o-magic. Even though it doesn't yet offer actual assembly capabilities, it can help you organize your in-place modeled parts.

Part-o-magic's tools to disable Observer were made to allow you continue to use projects you made with PoM, even when FreeCAD progress renders PoM obsolete. So you can at least be a little bit confident that ShapeGroup feature won't quickly go bust.