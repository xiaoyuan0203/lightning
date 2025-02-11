###################
App Component Tree
###################

**Audience:** Users who want to know how components can be composed with each other.

**Level:** Basic

****************************************
What is an Application Component Tree?
****************************************

Components can be nested to form component trees where the LightningFlows are its branches and LightningWorks are its leaves.

This design enables users to organize and maintain their code with more ease, but more importantly, this helps creating an ecosystem with re-usable components.

Here's a basic application with four flows and two works (associated tree structure):

.. figure:: https://pl-flash-data.s3.amazonaws.com/assets_lightning/tree.gif
    :alt: Basic App Components
    :width: 100 %


.. literalinclude:: ../code_samples/quickstart/app_comp.py

A Lightning app runs all flows into a single process. Its flows coordinate the execution of the works each running in their own independent processes.

***********************************************
How do I define my application component tree?
***********************************************

In order to define your application component tree, you need create a tree of components and attach them to your root flow.

You can attach your components in the **__init__** method of a flow.

.. code-block:: python

    import lightning_app as la


    class RootFlow(lapp.LightningFlow):
        def __init__(self):
            super().__init__()
            self.work = Work()  # The `Work` component is attached here.

            self.nested_flow = NestedFlow()  # The `NestedFlow` component is attached here.

Once done, simply add the root flow to a Lightning app as follows:

.. code-block:: python

    app = lapp.LightningApp(RootFlow())


******************************************
Is my application component tree static?
******************************************

No, Lightning supports dynamic flows and works.

You can simply attach your components in the **run** method of a flow using the Python functions **hasattr**, **setattr**, and **getattr**.

.. code-block:: python

    class RootFlow(lapp.LightningFlow):
        def run(self):

            if not hasattr(self, "work"):
                setattr(self, "work", Work())  # The `Work` component is attached here.
            getattr(self, "work").run()  # Run the `Work` component.

            if not hasattr(self, "nested_flow"):
                setattr(self, "nested_flow", NestedFlow())  # The `NestedFlow` component is attached here.
            getattr(self, "wonested_flowrk").run()  # Run the `NestedFlow` component.


But it is usually more readable to use Lightning built-in :class:`~lightning_app.structures.Dict` or :class:`~lightning_app.structures.List` as follows:

.. code-block:: python

    from lightning_app.structures import Dict


    class RootFlow(lapp.LightningFlow):
        def __init__(self):
            super().__init__()
            self.dict = Dict()

        def run(self):
            if "work" not in self.dict:
                self.dict["work"] = Work()  # The `Work` component is attached here.
            self.dict["work"].run()

            if "nested_flow" not in self.dict:
                self.dict["nested_flow"] = NestedFlow()  # The `NestedFlow` component is attached here.
            self.dict["nested_flow"].run()
